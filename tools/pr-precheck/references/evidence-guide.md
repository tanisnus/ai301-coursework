# Evidence guide: where evidence lives in a PR package

<!--
My map, filled with calib-01 open beside it. Each family says where
the evidence lives in an eval bundle, where it lives in my own PR in
live mode (plan.md, the branch diff, test_evidence.md, pr_draft.md),
and what good looks like.
-->

## Plan fidelity (harness category: silent-drift)

Where it lives, eval bundle: the "Plan context" block holds the plan:
its approach steps, its "Scope, one bounded change" line, its "Not in
scope" line, its "Files:" line, and any deviation note. The candidate
PR's "Diff" section (the `--- a/` / `+++ b/` file headers and hunks)
and "Commits" list show what actually changed. The "Description" holds
the fidelity claims ("implements the plan on #N", "no changes beyond
it"). In calib-01: the plan names one file (`themes.gitconfig`) and
one step (delete the shadowed `#434C5E` line); the diff touches only
that file and removes only that line.

Where it lives, live mode: `plan.md` (its Approach, Files, Scope /
not-in-scope sections, and `## Deviations`), read against `git diff
main...HEAD --stat` for the file list and `git diff main...HEAD` for
the hunks; the fidelity claims are in `pr_draft.md`'s Summary and
Changes sections.

What good looks like: every changed file in the diff is named in the
plan's Files line or a deviation note, or is a test or changelog that
supports a named step; every plan step shows up as a hunk, or its
absence is stated with a reason. The description's Changes bullets
map one-to-one onto the diff's changes. Silent drift looks like: a
hunk in a file the plan never names (a dependency bump, a second
refactor), a plan step with no hunk and no note, a different mechanism
from the plan's approach, or a description saying "exactly the plan"
over a diff that does more.

## Test evidence (harness category: not-tested)

Where it lives, eval bundle: the candidate PR's "Test evidence"
section, read against the "Plan context" block's "Test plan:" sentence
(each named case and its expected result) and the "Repro evidence"
line (the original failing command and output). The repo's named
checks are in the repo-facts `pull requests:` and `contribution
policy` lines ("pass cargo test", "tests added and passed",
"pre-commit"). In calib-01: the test plan asks for a clean
configparser parse and a `delta --show-syntax-themes` smoke check; the
evidence shows the configparser command with `# before:
DuplicateOptionError` and `parsed OK` after, plus the smoke check
stated.

Where it lives, live mode: `test_evidence.md` (the before run on
`main`, the after run on the branch, and the pasted output of `make
test-unit`, `make test-integration`, `make lint`, `make typecheck`),
read against `plan.md`'s Test plan; the description's Testing section
and its ticked boxes in `pr_draft.md`.

What good looks like: the plan's repro command re-run on the branch
with its actual after output visible and matching the plan's expected
result, the before shown or quoted for contrast, every case the test
plan names accounted for (observed, or not run with a stated reason),
and each repo check's outcome shown, including an honest failure or
an expected "no tests ran". Not tested looks like: "(none provided)",
"should fix it", "tests pass" with no output for the fix, a ticked
box with no matching output, or a named repro case that never appears.

## Diff quality (harness category: unreviewable)

Where it lives, eval bundle: the candidate PR's "Diff" section, hunk
by hunk, and the "Commits" list (commit messages like "gofmt",
"cleanup", "bump X", "hopefully" are a tell that a hunk is not the
fix). In calib-01: one hunk, one removed line, one commit naming it,
nothing else.

Where it lives, live mode: `git diff main...HEAD` hunk by hunk and
`git log --oneline main..HEAD`; `git diff main...HEAD --stat` should
list only the files the fix changes.

What good looks like: a reviewer can see the fix in each hunk, and
every hunk is the fix, its test, or an asked-for changelog/doc entry.
Debris looks like: added `print`/`console.log`/`dbg!` lines,
commented-out code, `TODO`/`FIXME`/"temp" markers, whitespace or
reflow changes to lines the fix did not need, rewriting or renaming
untouched logic (calib-03's `property.js` rewrite is the example), or
edits in files unrelated to the issue. An explained unrelated hunk is
still debris; it belongs in its own PR.

## Standards and comms (harness category: standards-wall)

Where it lives, eval bundle: the repo-facts block's `pull requests:`
line (template sections, checklist items, title conventions, changelog
asks, gating notes such as "not accepting new features") and
`contribution policy` line (the AI-use policy, if any), read against
the candidate PR's "Title" and "Description" and, for changelog or
test asks, the "Diff". Maintainer direction lives in "Thread
highlights" (MEMBER / maintainer comments). In calib-01: no PR
template; the docs ask only that PRs stay small and reference the
issue, and the description references `#2211`; no AI policy, so no
disclosure is needed.

Where it lives, live mode: `.github/PULL_REQUEST_TEMPLATE.md` (Summary,
Issue with `Closes #`, Changes, Testing checklist including the xfail
marker item, Screenshots / Demo, Notes for Reviewers),
`docs/CONTRIBUTING.md` (branch naming, conventional commits, checks to
run, remove the seeded bug's xfail marker), `scope.md` house rules,
and the course rule that AI use is disclosed (in Notes for Reviewers);
read against `pr_draft.md`. Maintainer direction lives in the issue's
comment thread.

What good looks like: every template section is present with real
content (or "N/A" plus why), the issue is linked in the asked form,
every applicable checklist ask is met in the diff or description, any
gating ask is honored, and when the policy requires it, the
description says plainly that AI was used, which tool, and how much.
Explicit maintainer direction from the thread is followed or engaged
with a reason. A standards wall looks like: a template section deleted
or left as a placeholder, a missing changelog entry the repo asks for,
no `Closes #N`, a feature PR to a repo that says it is not accepting
features, or no AI disclosure where the policy requires one. (Whether
the description's claims match the diff is plan fidelity, above.)
