# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

[The individual Path Review issue page. A link to the repository or the issue list
does not satisfy this field.]

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/35


**Verdict output**

[Your skill's live-mode output for this issue, pasted verbatim and ending with the
fenced JSON verdict block. A summary does not satisfy this field.]

**The verdict must record** `accept` **for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

```
# paste the output here, including the closing JSON block

 Skill(issue-select)
Successfully loaded skill

I'll start by reading the skill's scope and rubric.

  Read 2 files

Rubric is filled in. Now let me read the evidence guide and start gathering.

  Read 1 file, ran 1 shell command

Now the repo-level evidence.

  Ran 7 shell commands

⏺ Auto mode lets Claude handle permission prompts automatically — Claude checks each tool call for risky actions and prompt injection before executing. Actions Claude identifies as safe are executed, while actions Claude identifies as risky are blocked and Claude may try a different approach. Ideal for long-running tasks. Sessions are slightly more expensive. Claude can make mistakes that allow harmful commands to run, it's recommended to only use in isolated environments. Shift+Tab to change mode.

All required checks pass. Here's the read-out.

Issue #35 — "Implement a webhook system that notifies users when their review is ready" (codepath/pathreview-ai301-fa26-s3, in scope). Graded live against today, 2026-09-20.

┌────────────────────────┬───────┬──────────────────────────────────────────────────────────┐
│         Check          │ Grade │                         Evidence                         │
├────────────────────────┼───────┼──────────────────────────────────────────────────────────┤
│ maintainer_active      │ pass  │ 3 of the last 5 main commits dated 2026-09-16 — 4 days   │
│                        │       │ old, inside 30                                           │
├────────────────────────┼───────┼──────────────────────────────────────────────────────────┤
│ repo_in_use            │ pass  │ isArchived: false; newest commit 2026-09-16, inside 60   │
├────────────────────────┼───────┼──────────────────────────────────────────────────────────┤
│ unclaimed              │ pass  │ assignees: [], zero comments, timeline holds only 3      │
│                        │       │ labeled events, no linked PRs                            │
├────────────────────────┼───────┼──────────────────────────────────────────────────────────┤
│ scoped_for_newcomer    │ pass  │ Body names the change and its landing: api/routes/ (new  │
│                        │       │ webhooks.py), core/services/ (new webhook_service.py)    │
├────────────────────────┼───────┼──────────────────────────────────────────────────────────┤
│ still_open_and_fixable │ pass  │ state: OPEN; no PR in the repo references it             │
├────────────────────────┼───────┼──────────────────────────────────────────────────────────┤
│                        │       │ No AI clause in docs/CONTRIBUTING.md,                    │
│ ai_policy              │ pass  │ .github/PULL_REQUEST_TEMPLATE.md, or repo root — silence │
│                        │       │  passes                                                  │
├────────────────────────┼───────┼──────────────────────────────────────────────────────────┤
│ stack_match            │ pass  │ Python/FastAPI backend; repo primary language Python     │
│ (preferred)            │       │ (410 KB)                                                 │
├────────────────────────┼───────┼──────────────────────────────────────────────────────────┤
│ clear_repro            │ fail  │ Enhancement with no steps and no concrete example — no   │
│ (preferred)            │       │ payload shape, no registration example                   │
└────────────────────────┴───────┴──────────────────────────────────────────────────────────┘

One tension worth naming, not acting on. The issue is labeled tier-3 ("Advanced difficulty") and self-estimates 8–12 hours, and it asks for a whole subsystem — two new modules, callback registration, delivery. That is not first-issue sized. But your scoped_for_newcomer check tests locatability ("names the expected change and where it lives"), not size, and its two fail clauses — umbrella/tracking list, and unsettled design — don't fire: there's no sub-item list and no thread debating the design. So it passes as written, and the verdict is accept. If you want issues like this filtered out, that's a rubric edit (a size or difficulty-label gate), not a grading call.

Also: the fit profile in scope.md is still the placeholder (Write a few sentences here.). It didn't matter here — one candidate, nothing to rank — but ranking won't work until you fill it in.

{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/35",
  "checks": [
    {"name": "maintainer_active", "grade": "pass",
     "evidence": "3 of the last 5 main commits are dated 2026-09-16, 4 days before the 2026-09-20 grading date (within 30)"},
    {"name": "repo_in_use", "grade": "pass",
     "evidence": "isArchived: false and newest main commit 2026-09-16, within 60 days"},
    {"name": "unclaimed", "grade": "pass",
     "evidence": "assignees: [], comments: [], timeline shows only 3 'labeled' events, and no PR in the repo references issue 35"},
    {"name": "scoped_for_newcomer", "grade": "pass",
     "evidence": "Body names the change and its landing: 'Add a webhook endpoint where clients can register a callback URL' in 'api/routes/ (new webhooks.py)' and 'core/services/ (new webhook_service.py)'; not an umbrella and no open design debate"},
    {"name": "still_open_and_fixable", "grade": "pass",
     "evidence": "state: OPEN, closedAt: null, and no merged PR references it"},
    {"name": "ai_policy", "grade": "pass",
     "evidence": "No AI clause in docs/CONTRIBUTING.md, .github/PULL_REQUEST_TEMPLATE.md, or repo root; silence passes"},
    {"name": "stack_match", "grade": "pass",
     "evidence": "FastAPI backend issue in a repo whose primary language is Python (410 KB)"},
    {"name": "clear_repro", "grade": "fail",
     "evidence": "Enhancement body gives no reproduction steps and no concrete example payload or registration call"}
  ],
  "verdict": "accept"
}
```

---



## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

agreement: 2/3 scored items
agreement: 0/1 scored items
agreement: 2/3 scored items
agreement: 1/1 scored items
agreement: 18/20 scored items  (bar: 18/20: PASS)

**Issue analysis**

issue-15. Gold label: reject. My rubric: accept. The issue body names a concrete change — split Slack-compatible outgoing webhook fields into `command` and `text` — so `scoped_for_newcomer` passed (named change and where it lives). The repo was active, unclaimed (linked PRs are closed), and AI policy is conditions-not-a-ban. Gold rejects it as years of design debate with two abandoned PRs. My scope check only fails umbrellas and unsettled product decisions, so a named webhook split still passed.

**Check rationale**

`scoped_for_newcomer` | Issue body and labels | The issue names the expected change and where it lives (a page, file, or component). A docs outline that lists pages to add or update is enough. Fail if it is a tracking list or umbrella with no single landing, or if the desired behavior is still an open design or product decision | required

I rewrote this after `issue-01` (gold accept) failed the first wording. The old pass condition talked about "deep knowledge" and "design decisions," and Sonnet used that to reject a conda docs outline that already listed the pages to add. The current form matches the gold note on `issue-01`: a stated home and scope is enough.

**Trade-offs**

That wording accepted gold-reject `issue-15` (named webhook split, long debate) and rejected gold-accept `issue-19` (maintainer-named proof-mode performance bug; the run note is `failed: scoped_for_newcomer`). I left the check. The confirming full run was `agreement: 18/20 scored items  (bar: 18/20: PASS)`.

---



## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. Issue #35 is Python/FastAPI, which I already use, and it is a webhook/API feature rather than a giant frontend rewrite. The ticket itself says 8–12 hours and tier-3, so it is more time than a docs fix, but it is still a bounded pair of new files I can work through in this unit window.

2. The verdict was right that the repo is alive, the issue is open and unclaimed, and the body names `api/routes/webhooks.py` and `core/services/webhook_service.py`. What I weighed that the rubric does not: size and difficulty. The skill only checks that the landing is named, not that tier-3 / 8–12 hours is a lot for a first contribution.

3. Claiming should be easy. There are no comments, no assignee, and no linked PR. The Path Review house rule also says classmate claim comments would not block me. The harder part is doing the work, not getting the issue.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.