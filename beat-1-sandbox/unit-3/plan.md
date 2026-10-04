# Plan: issue #35 — webhook notification on review completion

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/35

## Diagnosis

The reproduction posted in Unit 2 confirms there is no notification path at
all, not just a missing log line:

> No line in that sequence, or anywhere else in the log, shows an outbound
> HTTP call, webhook dispatch, or any notification. The only reason this
> session found out the review had finished was polling `GET
> /reviews/{id}/status` again at `18:45:09`, 8 seconds after completion

> The repo-wide search confirms this isn't just a missing log line:
> `grep -rli "webhook\|callback\|notify\|notification" api/ core/` returns
> zero files. `core/services/review_service.py`'s `process_review()`, the
> function that flips `status` to `"complete"`, has no notification step
> in it at all.

I also checked whether an existing delivery or retry helper elsewhere in
`core/services/` could be reused instead of adding a new one, as I said I
would in the claim comment. `core/services/profile_service.py` has no such
pattern (`create_profile`, `get_profile`, `update_profile`, `delete_profile`
only), and `httpx` is already a project dependency (in `pyproject.toml`) but
unused anywhere in `core/` or `api/`. There is nothing to reuse; a new
`webhook_service.py` is the right call, matching the issue's own suggested
file.

## Scope

One bounded change: a client can register a callback URL against a specific
review, and it receives the review payload by POST once the review reaches
a terminal status (`complete` or `failed`) — either right away if it's
already terminal when registered, or from `process_review()` when it gets
there.

In scope:
- A new endpoint to register a callback URL on an existing review, at any
  point in its lifecycle.
- Storing that URL on the review row.
- Delivering exactly once per review: at registration time if the review is
  already terminal, otherwise from whichever of `process_review()`'s
  terminal branches the review actually reaches.

Not in scope, with reasons:
- **Retry/backoff on delivery failure.** No existing retry pattern exists
  anywhere in `core/services/` to extend, and designing one is a separate
  decision (attempts, backoff schedule, dead-lettering) that the issue
  doesn't ask for. V1 attempts delivery once and logs the outcome; a
  follow-up issue can add retries once real failure rates are known.
- **Webhook signing/authentication (e.g. HMAC signatures).** Not mentioned
  in the issue, and it's a separable hardening step, not part of closing the
  reported gap (no notification at all).
- **Multiple callback URLs per review, or webhooks for other events**
  (profile updates, etc.). The issue's repro and body are both about one
  review's completion; broadening to other event types is a different
  feature.
- **Accepting the callback URL directly on `POST /reviews`** (my Unit 2
  classmate's repro, `einnuian`, tried this: the 404 they hit was actually
  on `POST /webhooks`, and `POST /reviews` had silently accepted and
  ignored their `callback_url` field, since `ReviewCreate` doesn't define
  it). I'm keeping registration as its own endpoint, matching the issue's
  named file (`api/routes/webhooks.py`, separate from `reviews.py`).
  Checking my own reproduction's timing (17ms from `pending` to `complete`
  with `LLM_PROVIDER=mock`) against this design surfaced a real gap in an
  earlier draft: a client that only registers after creating the review
  could lose the race against a review that finishes first. I'm closing
  that by having the registration endpoint check the review's current
  status and deliver immediately if it's already terminal, rather than
  rejecting late registrations — see Approach step 2.

## Files

- `alembic/versions/<new>_add_callback_url_to_reviews.py` — new migration,
  adds a nullable `callback_url` column to `reviews`.
- `core/models/review.py` — add `callback_url: Mapped[str | None]`.
- `api/schemas/review.py` — add a `WebhookRegister` request schema
  (`callback_url: HttpUrl`) and include `callback_url` in `ReviewResponse`.
- `api/routes/webhooks.py` (new) — `POST /reviews/{review_id}/webhooks`,
  registers the callback URL on the named review, delivering immediately if
  the review is already terminal.
- `core/services/webhook_service.py` (new) — `deliver_webhook(callback_url,
  payload)`, one `httpx` POST with a short timeout, returns success/failure,
  logs either way. No retry loop.
- `core/services/review_service.py` — in `process_review()`, at each of its
  four exit points that set a terminal status (`complete`; `failed` for a
  missing profile; `failed` for failed safety checks; `failed` in the outer
  `except`), re-check the review's `callback_url` from the database and
  call `deliver_webhook()` if one is set.
- `api/main.py` — register the new router
  (`app.include_router(webhooks.router)`).
- `tests/unit/test_review_service.py` and a new
  `tests/unit/test_webhook_service.py` — regression tests for delivery and
  for the registration endpoint.

## Approach

1. Add the `callback_url` column via an Alembic migration (following
   `002_add_error_message_to_reviews.py`'s shape) and the model field.
2. Add `WebhookRegister` to `api/schemas/review.py`, and add
   `POST /reviews/{review_id}/webhooks` in the new `api/routes/webhooks.py`,
   which loads the review (404 if not found or not owned by the caller),
   sets `callback_url` and commits, then calls `await
   db.refresh(review)` before checking `review.status` — writing our own
   change first and only then re-reading is what lets this catch a
   concurrent completion instead of missing it, since `expire_on_commit=
   False` means a plain re-`select()` on the same session would just hand
   back the already-loaded object rather than its current row. If the
   refreshed status is already `"complete"` or `"failed"`, call
   `deliver_webhook()` immediately with the current review payload before
   returning; otherwise return, leaving delivery to `process_review()`.
   Registration always succeeds (200); there's no rejection case tied to
   timing.
3. Write `core/services/webhook_service.py`: `deliver_webhook(callback_url,
   payload)` does one `httpx.AsyncClient().post(callback_url,
   json=payload, timeout=5.0)`, logs `webhook_delivered` or
   `webhook_delivery_failed` (with the status code or exception), and never
   raises — a delivery failure must not affect the review's own stored
   status. Build `payload` with `ReviewResponse.model_validate(review)
   .model_dump(mode="json")`, not the model object itself, since `httpx`'s
   `json=` can't serialize the raw UUID/datetime fields.
4. Wire the call into `process_review()` at all four places it sets a
   terminal status and commits (the `complete` branch; the `failed` branch
   when the profile isn't found; the `failed` branch when safety checks
   fail; the outer `except`'s `failed`). At each, after that branch's own
   status commit, call `await db.refresh(review, ["callback_url"])` — a
   plain re-`select()` on the same long-lived session won't do it, since
   `expire_on_commit=False` means SQLAlchemy's identity map just hands back
   the object already loaded at the top of this function, not its current
   row — and call `deliver_webhook()` if the refreshed `callback_url` is
   set.
5. Register the router in `api/main.py`.
6. Add regression tests: registering a callback before completion, then
   completing the review, fires exactly one POST with the review's `id`
   and `status`; registering after the review is already complete fires
   the POST immediately from the registration call itself; a review with
   no callback registered completes with no POST attempted anywhere.

## Test plan

Re-run the Unit 2 repro steps against the built change, with one addition
right after step 5 (creating the review): register a callback URL for the
created review, and start a local listener to receive it, mirroring the
local listener my classmate's repro on this issue already used. Because
registration delivers immediately when the review is already terminal,
this holds regardless of how fast the review actually completes — which
matters here, since the Unit 2 repro measured completion at 17ms with
`LLM_PROVIDER=mock`, almost certainly faster than a second HTTP call could
land.

Expected after the fix, replacing the Unit 2 "Actual Behavior":
- The local listener receives exactly one `POST`, whose JSON body's `id`
  matches the `review_id` from step 5 and whose `status` is `"complete"` —
  whether that POST arrives from the registration call itself (if the
  review had already finished) or from `process_review()` (if it hadn't).
- Polling `GET /reviews/{review_id}/status` still works exactly as before
  (unaffected) — this is the "before" behavior from Unit 2, now optional
  for a client using the webhook instead of necessary.
- A second review created without registering a callback completes with no
  POST attempted anywhere (confirms the new code path is opt-in and doesn't
  change the no-callback case).

## Risks and unknowns

- I have not yet measured how long `httpx`'s call adds to `process_review()`
  in the worst case (a slow or hanging callback endpoint); the 5-second
  timeout bounds it, but if that turns out to matter for the background
  task queue, moving delivery to its own background task (rather than
  inline in `process_review()`) is the fallback, and I'd flag that for
  review rather than silently changing scope.
- Delivering from both the registration endpoint (when already-terminal)
  and from `process_review()` (when not) closes the ordering race, as long
  as both sides write their own change and commit it *before* refreshing
  and reading the other side's field — that ordering is what rules out
  both sides missing the callback entirely. It doesn't rule out a
  narrower case: if a registration request and `process_review()`'s own
  terminal commit land close enough together, both refreshes could still
  see each other's write and each attempt delivery, double-sending the
  payload. I'm accepting that as a rare edge case for v1 rather than
  adding locking around it, since the issue doesn't ask for delivery
  guarantees and no existing pattern in this codebase handles that kind of
  race either.

## Deviations

Nothing changed. The build followed Approach steps 1-6 as written: the
migration, the `callback_url` model/schema fields, the
`POST /reviews/{review_id}/webhooks` endpoint with immediate delivery on an
already-terminal review, `webhook_service.deliver_webhook()`, the
write-then-refresh wiring at all four of `process_review()`'s terminal
branches, the `api/main.py` router registration, and the regression tests.

The plan itself went through real revision, but that happened before
posting, not during the build: the first draft I checked with `plan-check`
in live mode had a genuine bug (a registration-after-completion race, plus
a stale in-memory read from `expire_on_commit=False`), and I fixed both in
the plan before posting the comment on the issue. What's in this file and
what's posted upstream is what got built, with no gap between them.

The Test plan's expected outcomes all held when run against the real
stack: a callback registered immediately after creating a review received
exactly one POST (delivered from the registration request itself, since
the review had already completed by the time registration landed — the
exact race the plan accounts for), a review with no callback registered
produced no POST, and a callback registered after a review was already
complete still delivered immediately rather than being rejected. Full
commands and output are in `plan-and-implement.md`'s Evidence field.
