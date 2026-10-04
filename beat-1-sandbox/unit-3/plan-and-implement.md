# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

tanisnus

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/35#issuecomment-5920459427

Based on my reproduction above, I plan to add webhook delivery in two pieces.

First, a new endpoint, `POST /reviews/{review_id}/webhooks`, where a client registers a callback URL on a review it already created. I checked `core/services/` for an existing delivery or retry pattern to reuse instead of adding a new one, as I said I would in my claim comment. There isn't one. `profile_service.py` has no such helper. `httpx` is already a project dependency but unused anywhere in `core/` or `api/`.

My reproduction measured a review going from `pending` to `complete` in 17ms with `LLM_PROVIDER=mock`. A second HTTP call to register a callback almost certainly can't land that fast. So I'm not treating "review already finished" as a rejection case. If a client registers after the review is already `complete` or `failed`, the endpoint will deliver the payload right away as part of that request, instead of returning an error. If the review is still in progress, the callback URL gets stored, and `process_review()` will deliver it once the review reaches `complete` or `failed`.

I'll wire that delivery into all four places `process_review()` can end: success, missing profile, failed safety checks, and the outer exception. At each one I'll write my own change and commit it first, then re-read the review's callback URL from the database, rather than trusting the copy already held in memory. That order is what should let a registration landing after this function's own load still get picked up.

I'm scoping out retry/backoff on delivery failure and webhook signing for this change. Neither is asked for in the issue. Building a retry policy with nothing existing to extend is a separate decision I'd rather not make silently inside a fix for the reported gap.

Test plan: I'll re-run my Unit 2 repro steps with a callback registered right after creating the review and a local listener running. I expect exactly one POST matching the review's id and completed status, whether the review had already finished by the time registration landed or not. I expect polling to still work unchanged. And I expect a review created with no callback registered to complete with no POST attempted.

Two things I'm flagging rather than deciding quietly. I haven't measured how much delay a slow or hanging callback endpoint would add inside `process_review()` itself. A 5-second timeout bounds it, but if that turns out to matter I'd move delivery to its own background task instead. Separately, delivering from two different code paths, the registration endpoint and `process_review()`, is meant to close the race where a callback registered after completion would otherwise never fire. It leaves a narrower one: if a registration request and a review's completion land at nearly the same instant, both paths could end up delivering. I'm accepting that double-delivery case as a rare edge for a first version rather than adding locking for it.

---

## Your branch

**Branch**

feat/35-webhook-notifications

**Evidence**

### Before (Unit 2 reproduction, posted upstream)

Steps 1-6 as posted: create a review, poll `/status`, cross-reference the server log, and
grep the codebase for any notification mechanism.

```
The review was created at 2026-09-27T18:45:01.716935Z (status: pending). The
server's own log shows the full pipeline ran and set status: complete at
18:45:01.733348Z, 17ms later, through this sequence, with no other log lines
interleaved:

review_processing_started      review_id=7caea7fa-15f3-4c0c-9900-617203a79d15
ingestion_pipeline_completed    review_id=7caea7fa-15f3-4c0c-9900-617203a79d15 sources_count=2
agent_orchestration_completed   review_id=7caea7fa-15f3-4c0c-9900-617203a79d15 sections_count=2
rag_retrieval_completed         review_id=7caea7fa-15f3-4c0c-9900-617203a79d15
safety_checks_passed
review_processing_completed     review_id=7caea7fa-15f3-4c0c-9900-617203a79d15 overall_score=0.81

No line in that sequence, or anywhere else in the log, shows an outbound HTTP
call, webhook dispatch, or any notification. grep -rli "webhook|callback|notify
|notification" api/ core/ returns zero files. core/services/review_service.py's
process_review() has no notification step in it at all.
```

### After (built change, re-run against `feat/35-webhook-notifications`)

Migration applied: `alembic upgrade head` → `Running upgrade 002 -> 003, Add
callback_url column to reviews table.`

Server started on the branch with the change (`uvicorn api.main:app`), a local
listener on `127.0.0.1:9999` standing in for the client's callback endpoint,
same seeded user/profile as Unit 2 (`user1@example.com`, profile
`2b74655b-812e-4e00-88d8-ea51c6b17e54`).

**Case 1 — register immediately after creating the review (races completion):**

```
$ curl -s -X POST http://localhost:8000/reviews -H "Authorization: Bearer $TOKEN" \
  -d '{"profile_id": "2b74655b-812e-4e00-88d8-ea51c6b17e54"}'
{"id":"540c5147-c723-4c58-bfad-248fbea66a3b","status":"pending", ...}

$ curl -s -X POST http://localhost:8000/reviews/540c5147-c723-4c58-bfad-248fbea66a3b/webhooks \
  -H "Authorization: Bearer $TOKEN" -d '{"callback_url": "http://127.0.0.1:9999/hook"}'
{"id":"540c5147-c723-4c58-bfad-248fbea66a3b","status":"complete", ...,
 "callback_url":"http://127.0.0.1:9999/hook", ...}
```

Listener log (`/tmp/webhook_listener_log.jsonl`) — exactly 1 line, matching the
review id and status `"complete"`:

```
{"id":"540c5147-c723-4c58-bfad-248fbea66a3b", ..., "status":"complete", "overall_score":0.81, ...}
```

Server log:
```
review_processing_started      review_id=540c5147-... request_id=49f12f19-...
review_processing_completed    review_id=540c5147-... overall_score=0.81 request_id=49f12f19-...
webhook_delivered               review_id=540c5147-... status_code=200 request_id=cb505362-...
webhook_registered_and_delivered_immediately  review_id=540c5147-... status=complete request_id=cb505362-...
```

The `webhook_delivered` line carries the *registration request's* request_id
(`cb505362`), not `process_review()`'s (`49f12f19`) — confirming delivery came
from the registration endpoint's own immediate-delivery path (the fix for the
race my first plan draft missed), not a lucky ordering.

**Case 2 — control: no callback registered:**

```
$ curl -s -X POST http://localhost:8000/reviews ... # review 9dde6d37-...
$ curl -s http://localhost:8000/reviews/9dde6d37-.../status
{"review_id":"9dde6d37-...","status":"complete","progress_pct":0}
$ wc -l /tmp/webhook_listener_log.jsonl
0
```
No callback registered, review completes exactly as it did before this change,
and the listener receives nothing. Confirms the change is opt-in.

**Case 3 — late registration, after the review already completed:**

```
$ curl -s -X POST http://localhost:8000/reviews/9dde6d37-.../webhooks \
  -H "Authorization: Bearer $TOKEN" -d '{"callback_url": "http://127.0.0.1:9999/hook"}'
{"id":"9dde6d37-...","status":"complete", ..., "callback_url":"http://127.0.0.1:9999/hook", ...}
$ wc -l /tmp/webhook_listener_log.jsonl
1
```
Registering after the review is already `complete` still succeeds (200, not a
rejection) and delivers immediately — the case the test plan named as the
reason two-endpoint registration needed the immediate-delivery path at all.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run, `--limit 3` (pkg-01, pkg-02, pkg-03): 3/3 agree. Partial run, no bar verdict.
2. Full run, 20 scored packages: 19/20 scored items (bar: 18/20: PASS). Category floor held
   in all five categories (clear-accept 6/7, scope-creep 4/4, thread-convention 2/2,
   unbuildable 3/3, wrong-cause 4/4). One disagreement: pkg-14 (see Package analysis).
3. Confirming run, `--save-run eval-run.txt`, no changes to rubric/evidence-guide/procedure
   since run 2: 19/20 scored items (bar: 18/20: PASS) — matches the agreement line in the
   committed `eval-run.txt`.

**Package analysis**

`pkg-14` (zellij-org/zellij#5174, category `clear-accept`). Gold: `accept`. My rubric:
`reject`, failing the `Executable` check.

The plan traces the reattach color-leak to "the client attach/reattach path in
`zellij-server`'s client connection handling before pane input is wired," backed by a
working `zellij --debug` trace that already shows where the leak originates, but says the
"exact functions to be pinned in the PR after tracing." My `Executable` check's pass
condition fails on any "to be pinned later" language, treating it the same as pkg-17's
genuine "gocui? tcell? not sure" — no location at all. Gold's own note calls this package
"arguable on the deferral," and reads it as accept because the plan has already narrowed
the problem to a specific path with a working diagnostic method; only the function boundary
inside that path is left open, not the approach or the file. My rubric can't currently tell
"traced to a location, boundary pinned later" apart from "don't know where to look" —
that's the real gap, not a wrong pass condition in general.

**Check rationale**

> Grounded-cause | The plan's stated diagnosis, read against the repro evidence's steps,
> control runs, and any debug output. | The stated cause explains the exact failing behavior
> the repro evidence shows, and is not ruled out by the repro evidence's own control run or
> debug output. If a control isolates a different mechanism than the one the plan names,
> this fails no matter how confidently the plan is written. | required

I revised this away from an earlier framing that only asked whether the stated cause
"explains the issue's behavior." That wording would pass a confident-sounding diagnosis that
never engages the repro evidence's own control run — exactly the failure mode in the four
`wrong-cause` packages: pkg-01's control (same request items, no `-v` flag, parses fine)
rules out the tokenizer theory; pkg-07's control shows the friendly-error instance method
still printing in the same build, ruling out "tree-shaken out"; pkg-11's control shows
`collect` already synthesizes null outside condition contexts, ruling that diagnosis out;
pkg-16's step-4 evidence shows the zeros are gone before any cast runs, so a post-read-cast
fix cannot restore them. All four are written confidently — pkg-16's gold note calls it
"long and confident." Naming "control runs, and any debug output" explicitly, and writing
the fail condition as "not ruled out by" rather than "explains," forces the check to
cross-reference the diagnosis against the evidence's own controls instead of judging
plausibility on the diagnosis's writing alone. It's why all four wrong-cause packages graded
correctly.

**Trade-offs**

The `Executable` check's current wording is the one that costs a package: it rejects pkg-14
because it can't distinguish "traced to a location with a working method, function boundary
open" from genuine not-knowing. I accept that miss in exchange for how the same wording
catches pkg-10, pkg-17, and pkg-18 (the `unbuildable` category, 3/3) without a loophole — a
vague plan can't earn a pass by gesturing at a broad file or module the way it could if the
check accepted "narrowed to a path" as sufficient. Tightening it to let pkg-14 through would
need a new distinction (a traced boundary vs. an unlocated one) and I'd want to re-check it
against pkg-10/17/18 as canaries before trusting it, which is why I left it as-is for this
submission rather than loosening it on one disagreement.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
