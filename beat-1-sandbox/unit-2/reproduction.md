# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

tanisnus

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/35#issuecomment-5843873312

Hello, I would like to take this issue as my Path Review contribution.

My plan is to add a webhook endpoint in `api/routes/webhooks.py`, backed by a new `core/services/webhook_service.py`, so a client can register a callback URL and receive a POST with the review payload once a long-running review finishes.

Before writing any code, I'll look at how a finished review currently notifies (or doesn't notify) callers. I'll also check for an existing delivery or retry pattern in `core/services/` I should reuse instead of adding a new one. I'll report back with what I find and a reproduction of the current gap.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/35#issuecomment-5859803528

### Environment:

- macOS 26.4 (BuildVersion 25E246, Apple Silicon)
- Docker 29.6.1 (Compose v5.3.0)
- Postgres 16.14 (`postgres:16-alpine`)
- Python 3.14.5 (project`.venv`)
-  FastAPI 0.141.1
-  Uvicorn 0.54.0
- SQLAlchemy 2.1.1.
- Fork commit `2f4e82f` on `main`. `LLM_PROVIDER=mock` (the `.env.example` default), so no external API key was needed for this reproduction.

### Steps:

1. `cp .env.example .env`, then `docker compose up -d` to start Postgres,
   Redis, and ChromaDB.
2. `make setup`, which installs deps, runs migrations, and seeds the DB with 3 sample
   users (including `user1@example.com` / `password1`, who already has a
   profile and prior reviews from the seed data).
3. Start the backend only: `uvicorn api.main:app --host 0.0.0.0 --port 8000`.
4. `POST /auth/login` with the seeded `user1@example.com` / `password1` to
   get a JWT.
5. `POST /reviews` with that user's existing `profile_id`
   (`2b74655b-812e-4e00-88d8-ea51c6b17e54`). Returns immediately with
   `"status": "pending"` and a `review_id`.
6. Poll `GET /reviews/{review_id}/status` once per second.
7. Cross-reference the server's own log for the same `review_id` and time
   window.
8. Separately, `grep -rli "webhook\|callback\|notify\|notification" api/
   core/` across the whole codebase.

### Expected (per issue #35)
A client should be able to register a callback URL
and receive a POST with the review payload once the review finishes, instead
of having to keep asking.

### Actual Behavior
The review was created at `2026-09-27T18:45:01.716935Z`
(`status: pending`). The server's own log shows the full pipeline ran and set
`status: complete` at `18:45:01.733348Z`, 17ms later, through this
sequence, with no other log lines interleaved:

```
review_processing_started      review_id=7caea7fa-15f3-4c0c-9900-617203a79d15
ingestion_pipeline_completed    review_id=7caea7fa-15f3-4c0c-9900-617203a79d15 sources_count=2
agent_orchestration_completed   review_id=7caea7fa-15f3-4c0c-9900-617203a79d15 sections_count=2
rag_retrieval_completed         review_id=7caea7fa-15f3-4c0c-9900-617203a79d15
safety_checks_passed
review_processing_completed     review_id=7caea7fa-15f3-4c0c-9900-617203a79d15 overall_score=0.81
```

No line in that sequence, or anywhere else in the log, shows an outbound
HTTP call, webhook dispatch, or any notification. The only reason this
session found out the review had finished was polling `GET
/reviews/{id}/status` again at `18:45:09`, 8 seconds after completion,
purely because that's when the next poll happened to run. A client that
polled less often, or not at all, would have no other way to learn the
review was ready.

The repo-wide search confirms this isn't just a missing log line:
`grep -rli "webhook\|callback\|notify\|notification" api/ core/` returns
zero files. `core/services/review_service.py`'s `process_review()`, the
function that flips `status` to `"complete"`, has no notification step
in it at all. The only two ways to observe a status change are polling
`GET /reviews/{id}/status` or `GET /reviews/{id}`.

This confirms the gap issue #35 asks to close: nothing currently notifies a
client when a long-running review finishes.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

agreement: 17/20 scored items (bar: 18/20: below the bar; category floor unmet: no match in disclosure)
agreement: 19/20 scored items (bar: 18/20: PASS)
agreement: 20/20 scored items (bar: 18/20: PASS)

**Package analysis**

pkg-20. Gold label: reject (category: disclosure). My rubric's first run: accept. Issue
#35's repo-facts state ghostty's contribution policy: "All AI usage in any form must be
disclosed, stating the tool used and the extent of the assistance." The candidate package
is an excellent repro on every proof check (environment, steps, matching behavior) but its
claim and repro comments never mention AI at all. My first rubric folded disclosure into a
single `Comms` check whose pass condition ended with "...and discloses AI assistance if the
repo's policy requires it" — a trailing clause after two other conditions. The grading
model verified specificity and template completeness, then didn't separately verify the
disclosure clause, and returned accept. After I split `Disclosure` out into its own
required row, forcing an explicit "check the policy, check for a disclosure statement" step
for every package, the second and third runs graded pkg-20 reject, matching gold.

**Check rationale**

`Disclosure` | The repo's contribution/AI-use policy (from the repo-facts block or repo
docs), read against the claim and repro comment text. | If the repo's policy requires
disclosing AI assistance (always, or under stated conditions that apply here), the comment
states that AI was used and the extent of the assistance. If the policy has no such
requirement, or its condition doesn't apply, this passes without a disclosure statement.
Silence when disclosure is required is a fail, not an accept. | required

I split this out of a combined `Comms` check after the pkg-20 miss above. My worksheet's
original rubric had a fifth check, `AI-usage`, that graded writing style (no dashes, no
fancy words, no excessive enthusiasm) with no measurable threshold — my calibration partner
graded it `?` on `calib-03` for exactly that reason. Rather than inventing an arbitrary
style-counting threshold, I moved that concern to `voice-guide.md` (personal, live-mode
only, never scored) and rewrote the rubric slot as this evidence-based `Disclosure` check
instead: a policy either requires a disclosure statement or it doesn't, and the comment
either has one or it doesn't. That's a fact a stranger can check, not a style judgment.

**Trade-offs**

Broadening `Behavior-matches` to accept an honest, specific cannot-reproduce cost me
strictness elsewhere it could have mattered. My first version of that check required the
artifact to show the issue's actual failure behavior, full stop — which correctly rejects
wrong-target packages, but also blanket-failed `pkg-09` and `pkg-10` (both gold `accept`,
category `clear-accept`, both honest "I could not reproduce this, here's what I tried and
what differed" write-ups with real artifacts). I rewrote the pass condition to accept a
documented, specific non-repro as a pass, which fixed both. The trade-off: a sloppier
"I couldn't reproduce it, must be my setup" report with a thin artifact could now read
closer to a pass than under the stricter version. I accepted this because the final full
run still held `wrong-target` at 4/4 and `no-evidence` at 4/4 — the packages that check
exists to catch, a vague or missing artifact still fails the check as written, so the
loosened wording didn't visibly buy back a miss anywhere else in this eval set.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
