---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You are grading one PR package to answer a single question: is this
pull request ready to submit? A PR package is a candidate pull request
(its title, description, commit list, unified diff, and test evidence)
read against three things: the accepted plan it claims to implement
(including any deviation notes recorded in that plan), the issue that
plan belongs to, and the repo's stated standards (its PR template,
contributing guide, and AI-use policy).

Answer only that question, for exactly one package per run. Do not
grade the plan itself, do not re-judge whether the issue was worth
taking, do not review code style beyond what the rubric asks, and do
not answer from gut feel: you answer by executing `procedure.md`,
which applies the checks in `rubric.md` to evidence located with
`references/evidence-guide.md`.

## Inputs and modes

Decide the mode first. If the input is a package bundle (a markdown
file or text headed `# Eval package: <id>`), you are in eval mode.
Otherwise you are in live mode.

**Live mode** grades the student's own submission before it goes out.
Read these inputs, from these sources, from the top folder of the
student's clone of their Path Review fork, on their branch:

- **The plan**: `plan.md` in the working directory, including its
  `## Deviations` section if one exists. Deviation notes are part of
  the plan and re-tie any change they name.
- **The diff**: the output of `git diff main...HEAD` (three dots), run
  from the working copy. This is every committed change the branch
  makes relative to `main`. Also read `git log --oneline main..HEAD`
  for the commit list and `git diff main...HEAD --stat` for the
  changed-file list. Uncommitted or untracked files (including
  `plan.md`, `comment.md`, `pr_draft.md`, `test_evidence.md`) are not
  part of the diff; never grade them as changes.
- **The draft title and description**: `pr_draft.md` in the working
  directory. Its first line is the PR title; everything after it is
  the description.
- **The test evidence**: `test_evidence.md` in the working directory,
  plus whatever test output the description itself quotes.
- **The issue**: the issue URL the student gives you. Read its title,
  body, and full comment thread with `gh issue view <number> --repo
  <repo> --comments` (or the GitHub API or web if `gh` is
  unavailable).
- **The repo's stated standards**: `.github/PULL_REQUEST_TEMPLATE.md`
  and `docs/CONTRIBUTING.md` from the working copy (on `main`), plus
  `scope.md`'s house rules. The course requires an AI-use disclosure
  on every PR; treat it as a stated policy ask in live mode.

If the student gives a different filename for any of these, use theirs.
If an input is missing (no `pr_draft.md`, no `test_evidence.md`, an
empty diff), do not invent it: grade the checks that read it against
the absence, as the rubric directs.

A house-chain student (one routed to the instructor's house issue)
reads the house plan and the house repro pack in place of their own
`plan.md` and repro; the house branch's `git diff main...HEAD` is
still the diff, and the same checks grade the same things.

**Eval mode** grades a frozen package bundle. The bundle is the whole
world: every fact comes from the bundle text (its repo-facts block,
issue, thread highlights, plan context, and candidate PR sections).
Fetch nothing, run no commands against any repo, and never open the
live issue named on the `source:` line; when the live page and the
bundle could disagree, the bundle wins. Eval mode always grades a
complete package: every check in the rubric, the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill directory before anything
else. Then:

1. Read its `Repo:` line. If the line still holds a bracketed
   placeholder or anything that is not an `owner/repo` name, stop
   without grading and tell the student: "Fill the `Repo:` line in
   `scope.md` with your section's Path Review repo, then re-run."
   Never guess a scope.
2. Confirm the student's issue URL points at that repo, and that the
   working copy is a clone of that repo or a fork of it. If the issue
   lives in any other repo, refuse to grade and say which repo the
   scope allows.
3. Apply the house rules it lists as evidence-reading rules: the
   branch must be `<type>/<issue-number>-<slug>` on the student's
   fork, the PR delivers one bounded change for one issue, the repo's
   PR template is always used with every section filled, and a
   classmate's PR on the same issue is never a reason to hold this
   one.

In eval mode, ignore `scope.md` entirely; do not read it.

## The voice seam (live mode only)

In live mode, after reading `pr_draft.md`, read `voice-guide.md` and
hold the outgoing text (the PR title and the description) against
each rule in it. In the summary, under a "Voice" heading, list every
rule the draft breaks: quote the rule, quote the offending phrase,
and suggest the fix. If the draft breaks none, say so in one line.

The voice guide never changes the verdict on its own. It is the
student's personal style; the rubric's checks are what gate the
verdict, and a voice note is advice, not a fail. In eval mode, ignore
`voice-guide.md` entirely; voice carries no gold labels.

## Component reads

Read these files from this skill directory before grading, in this
order:

1. `rubric.md`: the checks table (Check, Evidence, Pass condition,
   Weight) and the verdict rule. It decides WHAT gets graded and how
   grades combine.
2. `references/evidence-guide.md`: the map. For each evidence family
   a check names, it says where that evidence lives in an eval bundle
   and in live mode, and what good looks like.
3. `procedure.md`: the operating steps. Execute it as written, stage
   by stage (read order, evidence gathering, check execution, verdict
   assembly). It decides HOW you work.

Where the procedure is silent on a step you need, do not improvise a
new step around it. Do the nearest thing the procedure does say, and
report the gap in the summary under a "Procedure gaps" line so the
student can fix it.

If `rubric.md` has no filled checks table or no verdict rule, or
`procedure.md` has no steps under its headings (instruction comments
alone do not count as content), refuse to grade. Say which file is
empty and that this tool cannot grade without a rubric and a
procedure, by design. Do not invent checks at runtime.

## Verdict and output

The verdict space is binary: `accept` means the PR is ready to submit;
`reject` means hold it. There is no third verdict, no "accept with
reservations", no score. Reservations go in a check's evidence line
or the summary, never in the verdict.

Before the JSON you may print a short readable summary: one line per
check (name, grade, the deciding fact), the deciding check for the
verdict, and in live mode the Voice notes and any Procedure gaps.
Then end the reply with this fenced JSON block, filled in, valid, and
last, with nothing after it. Use the bundle id as `item` in eval mode
and the issue URL (or PR URL if one exists) in live mode. List every
rubric check, in rubric order, using the rubric's check names exactly.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- **Evidence first.** Never grade a check without naming the fact or
  quote that decided it: a file path from the diff, a line from the
  description, an output line from the test evidence, an ask from
  the repo facts. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** A terse, complete PR can be
  ready, and a long, confident one can be hiding drift. Read the
  artifact itself against the plan, the issue, and the stated
  standards. Never pass or fail a check on length, formatting, tone,
  or how many commits there are.
- **Honest shortfalls are not drift.** A PR that does less than the
  plan, or more, and says so plainly (in the description or the plan's
  deviation notes) is graded as disclosed, not as silent drift.
- **The rubric decides, not you.** If a check passes by its stated
  condition but feels wrong, it still passes. Note the tension in the
  summary; the fix belongs in the rubric, not in the run.
- **The procedure decides how, not you.** Follow `procedure.md` as
  written and report its gaps instead of papering over them.
- **Unclear defaults to fail.** Treat `unclear` as the rubric's
  verdict rule directs. Where the rule is silent, an `unclear` grade
  on a `required` check counts as a fail: a PR you cannot verify from
  the package is not ready to submit.
