# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| maintainer_active | Repo-facts block: the last 5 default-branch commit dates and the maintainer first-response sample. Do not use this issue's comment dates | At least 1 of the last 5 default-branch commits is within 30 days of the snapshot date. A quiet or years-old thread on this issue still passes if that commit test passes | required |
| repo_in_use | Repo-facts block: the `archived:` flag and the last 5 default-branch commit dates | `archived: no`, and at least 1 commit within 60 days of the snapshot date | required |
| unclaimed | Repo-facts assignee field, comment thread, and linked PRs | No assignee and no open linked PR. A "I'm working on it" comment fails only if it is dated within 90 days of the snapshot and a maintainer has not since invited new takers. Years-old claims, closed linked PRs, and a maintainer saying the issue is free all pass | required |
| scoped_for_newcomer | Issue body and labels | The issue names the expected change and where it lives (a page, file, or component). A docs outline that lists pages to add or update is enough. Fail if it is a tracking list or umbrella with no single landing, or if the desired behavior is still an open design or product decision | required |
| still_open_and_fixable | Issue state, comment thread, linked PRs | Issue is open and no merged PR already fixes it | required |
| ai_policy | Repo-facts contribution policy line | Pass unless the policy is an outright ban on AI-generated code or documentation. Disclosure, review, and "understand your PR" conditions pass. Silence (no policy stated) passes | required |
| stack_match | Issue body and repo language | Touches JS/TS/React, Node, or Python | preferred |
| clear_repro | Issue body | Includes steps to reproduce or a concrete example | preferred |

## Verdict rule

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict, they
rank accepted issues; unclear counts as fail." -->

Accept if every required check passes. Preferred checks never change the verdict and only rank accepted issues. An unclear grade on a required check counts as fail.
