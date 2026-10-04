# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Grounded-cause | The plan's stated diagnosis, read against the repro evidence's steps, control runs, and any debug output. | The stated cause explains the exact failing behavior the repro evidence shows, and is not ruled out by the repro evidence's own control run or debug output. If a control isolates a different mechanism than the one the plan names, this fails no matter how confidently the plan is written. | required |
| Bounded-scope | The plan's Scope and Files/Approach sections, read against the behavior the issue names. | The plan names one change that addresses the issue's behavior, states what is explicitly not in scope, and its files/approach do not carry unrelated rewrites, migrations, new abstractions, or while-in-the-area work beyond what fixing the named cause requires. Deferring related work with a stated reason does not fail this check. | required |
| Executable | The plan's Files and Approach sections. | Specific files or code locations and a concrete, ordered approach are named, such that someone who has never seen the plan could start work without asking the author a clarifying question. Placeholder language ("wherever it needs to go," "upstream or vendored, whichever is easier," "not sure which layer") fails this check. | required |
| Decisive-test | The plan's Test plan section, read against the repro evidence's steps and artifacts. | The test plan names a concrete, observable outcome tied to the fix itself (a specific re-run of the repro steps with an expected result, a specific assertion, a specific exit code or output) that would come out differently if the fix were wrong. A subjective outcome ("should feel fast," "shouldn't feel broken") or a plan to run the full suite with no outcome named for the fix itself fails this check. | required |
| Honest-uncertainty | The plan's stated risks, unknowns, or open questions, read against how the rest of the plan is written. | Any unknown the plan actually depends on (an unmeasured cost, an unconfirmed platform behavior, a deferred investigation) is stated as an unknown, not asserted as settled fact. The plan does not claim a diagnosis or an outcome with more certainty than its own evidence supports. | required |
| Thread-aware | The plan comment text, read against the thread highlights (live: the issue thread) for any explicit maintainer diagnosis, chosen approach, or rejected approach. | If the thread contains explicit maintainer direction, the plan comment engages it — follows it, or states a specific reason for diverging — rather than proposing an unrelated approach as if the thread said nothing. If the thread contains no such direction, this passes by default. | required |
| Disclosure | The repo-facts block's stated AI-use/contribution policy, read against the plan comment text. | If the repo's policy requires disclosing AI assistance (always, or under a stated condition that applies here), the plan comment states plainly that AI was used and the extent of the assistance. If the policy has no such requirement, or its condition doesn't apply, this passes without a disclosure statement. Silence when disclosure is required is a fail, not an accept. | required |

## Verdict rule

Accept (ready to post and build from) only if every required check passes.
This rubric has no preferred checks. `unclear` counts as a fail — a plan I
cannot verify from the package is a plan that is not ready to build from.
Any required check failing or unclear holds the package (reject).
