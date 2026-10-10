# Procedure: how this tool grades a PR package

<!--
Rewritten from my Unit 3 plan-check procedure for a PR package. The
order is built for side-by-side reads: the standards and the plan are
read and noted BEFORE the candidate PR, so the PR's own description
cannot quietly stand in for what the plan or the repo actually asked.
-->

## Read order

0. Live mode only: read `scope.md` first and apply SKILL.md's scope
   seam (stop on a placeholder `Repo:` line, refuse an out-of-scope
   repo). Eval mode skips this step.
1. Read the repo's stated standards. Eval: the repo-facts block
   (`pull requests:` and `contribution policy` lines). Live:
   `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and
   `scope.md`'s house rules. Write down as a numbered "asks list":
   every required template section, every checklist item, the
   issue-link form, any title or commit convention, any changelog or
   tests-with-changes ask, any gating ask (not accepting features,
   discuss first), every named test command or check, and the exact
   AI-policy wording (live: add "AI use must be disclosed" from the
   course rule).
2. Read the issue (title, body) and the thread (eval: thread
   highlights; live: the full comment thread). Note the one behavior
   the issue names, and note any explicit maintainer direction
   (chosen approach, rejected approach, constraint) or that there is
   none.
3. Read the plan context (eval: the "Plan context" block; live:
   `plan.md` with its `## Deviations` section). Write down as a "plan
   list": the repro's failing behavior, each approach step, each file
   or area named, each "not in scope" item, each deviation note, and
   each case the test plan names with its expected-after result.
   Reading the plan before the PR matters: Plan-fidelity and
   Repro-verified compare the PR against this list, and the list must
   be formed before the PR's description frames the change.
4. Read the candidate PR's diff and commit list (eval: "Diff" and
   "Commits"; live: `git diff main...HEAD`, `git diff main...HEAD
   --stat`, `git log --oneline main..HEAD`). Write down a "diff list":
   every changed file, and for each file a one-line summary of each
   hunk. Read the diff before the description, so you know what the
   change actually does before reading what it claims to do.
5. Read the test evidence (eval: "Test evidence"; live:
   `test_evidence.md` and the description's Testing section). Note
   each command run, its before and after output, and each check's
   reported result.
6. Read the title and description last (eval: "Title" and
   "Description"; live: `pr_draft.md`, first line is the title). Write
   down a "claims list": each factual claim about the change, each
   claim about testing, each ticked checkbox, the issue link, and any
   AI-use statement.
7. Live mode only: read `voice-guide.md` after the description and
   note broken rules for the summary (never for a check grade).

## Evidence gathering

Pull each check's facts from the lists made above; build these pairs
before grading anything:

- **Plan-fidelity**: pair the diff list against the plan list, both
  ways. For each changed file/hunk, write the plan step, plan file, or
  deviation note it serves, or "unplanned". For each plan step/file,
  write the hunk that delivers it, or "missing". For every "unplanned"
  or "missing" entry, search the description and the deviation notes
  for a disclosure and copy it if found.
- **Description-matches-diff**: pair the claims list against the diff
  list. For each claim about the change, write the hunk that makes it
  true, or "not in diff". Flag any fidelity claim ("exactly", "no
  changes beyond", "only") that sits next to an "unplanned" entry
  from the step above.
- **Repro-verified**: pair the plan list's test-plan cases against the
  test evidence. For each case, copy the observed after output (or
  the specific stated result) and the plan's expected-after side by
  side, or write "absent", or copy the stated reason it was not run.
- **Repo-checks-run**: pair the asks list's named test commands and
  checks against the test evidence, and pair the claims list's testing
  claims and ticked boxes against the test evidence. For each, copy
  the reported outcome, or write "no outcome shown".
- **Clean-diff**: walk the diff list hunk by hunk and copy any line
  that is a debug leftover, commented-out code, an uncertain marker,
  formatting/reflow churn, a refactor of code the fix does not need,
  or an edit in a file unrelated to the plan list.
- **Repo-asks-met**: for each item on the asks list, write "met"
  (quote where), "n/a" (say why it plainly does not apply), or
  "unmet". Template sections count as met only with real content.
- **AI-disclosure**: copy the AI-policy wording from the asks list
  and the AI-use statement from the claims list (or "none").
- **Thread-direction**: copy the maintainer direction from step 2 (or
  "none"), and the diff hunk or description sentence that follows,
  contradicts, or engages it.
- **Title-names-change**: copy the title.

## Check execution

Grade the checks in the rubric's table order: Plan-fidelity,
Description-matches-diff, Repro-verified, Repo-checks-run, Clean-diff,
Repo-asks-met, AI-disclosure, Thread-direction, Title-names-change.

Grade each check by applying its rubric pass condition to its gathered
pair only; do not re-read the whole package. Re-read one specific
section only when a gathered note is ambiguous about what the package
says.

When a check's evidence family is absent from the package, apply the
pass condition to the absence instead of defaulting to `unclear`:
no maintainer direction means Thread-direction passes; no AI policy
means AI-disclosure passes; no stated asks means Repo-asks-met passes;
but "(none provided)" test evidence means Repro-verified fails, and a
missing plan step with no disclosure means Plan-fidelity fails. Grade
`unclear` only when the package contains the evidence but it cannot
be read either way (for example, test output truncated before the
result line).

An "unplanned" hunk that is a test for the fix, a changelog entry the
repo asks for, or an import the fix needs counts as serving a plan
step, not as unplanned. A disclosure counts only if it names the
specific change or omission; a generic "some small cleanups" does not
disclose a specific unplanned hunk.

One fact may fail more than one check (an unplanned refactor can fail
Plan-fidelity, Description-matches-diff, and Clean-diff). Grade each
check on its own condition; do not skip a check because another one
already failed.

## Verdict assembly

Apply the rubric's verdict rule exactly: `accept` only if all eight
required checks grade `pass`; any required `fail` or `unclear` gives
`reject`. Title-names-change is reported and never counted.

The deciding check is the first required check, in rubric table
order, graded `fail` or `unclear`. Name it in the summary line before
the JSON. If every required check passed, the summary says the
verdict is `accept` with no deciding failure.

In the JSON, each check's `evidence` is one line quoting the gathered
fact that decided it: for a fail or unclear, the specific hunk, line,
missing case, or unmet ask (for example: "pkg/gui/gui.go reflow of
view setup is not in the plan"); for a pass, the fact that satisfied
the condition. Never restate the check's name as its evidence. The
same set of grades always produces the same verdict.
