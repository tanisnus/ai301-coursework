# Rubric: is this pull request ready to submit?

<!--
Built from my Unit 3 plan-check rubric, rewritten for a PR. Each check
maps to one harness failure family:
  Plan-fidelity, Description-matches-diff  -> silent drift
  Repro-verified, Repo-checks-run          -> not tested
  Clean-diff                               -> unreviewable
  Repo-asks-met, AI-disclosure,
  Thread-direction                         -> standards wall
Honest-outcome rule: a shortfall the PR discloses (in the description
or the plan's deviation notes) is not drift and does not by itself
hold the package. An unrelated change still fails Clean-diff even
when it is explained, because it still buries the fix.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Plan-fidelity | The diff's changed files and hunks, read against the plan's Scope, Files, Approach, and "not in scope" lines plus any deviation notes; then the plan's steps read back against the diff. | Both directions hold. (a) Every changed file and hunk is something the plan or a deviation note names, or something needed to deliver a named step (a test for the fix, a changelog entry the repo asks for, an import the fix needs). (b) Every step and file the plan names is present in the diff, OR its absence is disclosed in the description or a deviation note with a reason. A diff that does more than the plan (an extra feature, a dependency bump, a rewrite of a different mechanism, a change in a "not in scope" area) or less than the plan with no disclosure fails. A diff that implements a different mechanism from the plan's approach with no disclosure fails even if it might fix the issue. | required |
| Description-matches-diff | Each factual claim in the PR title and description about what the change does ("implements the plan exactly", "no changes beyond it", "tests added", "changelog added", "only touches X", the named approach), read against the diff. | Every claim the description makes about the change is true of the diff, and every substantive change in the diff is either described or is a test/changelog that supports a described change. A description that claims fidelity ("exactly the plan", "nothing else changed") while the diff contains an unplanned change, or names files, tests, or behavior the diff does not contain, fails. | required |
| Repro-verified | The test evidence section (live: `test_evidence.md` and the description's Testing section), read against the plan's Test plan and the repro evidence's own steps. | The plan's primary repro is re-run on the branch and the evidence shows (or states with a specific observed value) the after result, and that result is the plan's expected-after. Every distinct failure case or repro the plan's test plan names is accounted for: an observed after result, or a plain statement of why it could not be run. A named case that is simply absent fails. "None provided", "should fix it", "looks right", "hopefully", or a claim of passing with no observable result for the fix itself fails. | required |
| Repo-checks-run | The repo facts' (live: PR template's and `docs/CONTRIBUTING.md`'s) named test commands or checks (a suite, `cargo test`, `yarn test`, `make test-unit`/`lint`/`typecheck`, pre-commit), read against the test evidence; plus any testing claim or ticked testing box in the description. | If the repo names checks or a suite to run, the evidence reports their outcome: pass, or a failure stated honestly with its reason (an expected "no tests ran" counts when pasted as is). If the repo names none, a project test run is not required and this passes when the description makes no unbacked testing claim. Fails when a named check's outcome is missing, or when the description claims or ticks a result ("all tests pass", "lint passes") that the evidence does not show. | required |
| Clean-diff | The unified diff and commit list, read hunk by hunk. | Every hunk serves the fix or its supporting test/changelog/docs. Fails on any of: debug leftovers (`print`, `console.log`, `dbg!`, stray logging added for the author), commented-out code, placeholder or uncertain markers (`TODO`, `FIXME`, "hopefully", "temp"), formatting or reflow churn in lines the fix did not need to touch, refactors or renames of code the fix does not change, or drive-by edits in unrelated files. An unrelated hunk fails this check even when the description explains it; it belongs in its own PR. | required |
| Repo-asks-met | The repo facts' PR-template sections and checklist items and the contributing guide's PR asks (live: `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and `scope.md` house rules), read against the title, description, and diff. | Every stated ask that applies to this change is met: required template sections present with real content (not left as the template's placeholder), the issue linked in the asked form (`Closes #N`, `fixes #N`), a title in the asked convention, a changelog or whatsnew entry in the diff when asked, tests in the diff when the repo asks for tests with behavior changes, and any gating ask honored (a repo "not accepting new features" gets only a bugfix; an ask to discuss first is followed). An ask that plainly does not apply to this change (docs for a change with no API/CLI surface, config docs when no config was added) passes without being mentioned. Process steps a package cannot show (signing a CLA, a vouch flow) are not graded. If the repo states no asks, this passes. | required |
| AI-disclosure | The stated AI-use policy (eval: the repo facts' contribution policy; live: the course rule that every PR discloses AI use, plus any repo AI policy), read against the description. | If the policy requires disclosure, the description states plainly that AI was used, which tool, and the extent of the assistance. If the policy requires a human who understands the work, the description does not hand its correctness to the AI ("had claude look at it and this should fix it"). If the policy has no requirement, this passes with or without a disclosure. Silence when disclosure is required fails. | required |
| Thread-direction | The thread highlights (live: the issue's full comment thread) for explicit maintainer direction (a chosen approach, a rejected approach, a constraint such as "no private APIs"), read against the diff and description. | If a maintainer gave explicit direction, the diff follows it, or the description names it and gives a specific reason for diverging. If the thread has no maintainer direction, this passes by default. | required |
| Title-names-change | The PR title. | The title names the behavior the change fixes or adds, specifically enough that a maintainer scanning the PR list knows what it does ("Reject --threads values above a sane cap" passes; "fixed the bug!!" does not). | preferred |

## Verdict rule

`accept` (submit) only if every `required` check grades `pass`. Any
`required` check graded `fail` or `unclear` gives `reject` (hold):
`unclear` counts as `fail`, because a PR I cannot verify from the
package is not ready to submit. The `preferred` check (Title-names-change)
is reported but never changes the verdict. A shortfall the PR discloses
honestly (in the description or the plan's deviation notes) is graded
as disclosed by Plan-fidelity and Repro-verified, so disclosure alone
can carry a "less than everything" PR to `accept`.
