# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Env-recorded | The repro report's environment record, read against the tool version and OS the issue targets. | The report names a specific tool version and operating system — not "latest" or "my machine" — and any difference from the issue's stated target is called out, not left silent. | required |
| Input-matches | The repro report's stated input/trigger conditions, read against the input or conditions the issue describes. | The reproduction uses the same input/conditions the issue names (or a stated, explained equivalent), not a different or unstated input. | required |
| Behavior-matches | The output excerpt or artifact in the repro report, read against the failure behavior the issue describes. | The artifact shows the same failure behavior the issue describes, not an adjacent or superficially similar one. If the report instead states it could not reproduce the issue, this passes when the artifact documents a genuine, specific attempt and names exactly how the outcome differed from the issue's description — an honest, evidenced cannot-reproduce is not a failure here; only a missing, unspecific, or silently-mismatched artifact fails. | required |
| Steps-complete | The repro report's steps, from starting state through to the trigger. | A stranger could re-run the steps as written and reach the same trigger without guessing or filling in a missing step. | required |
| Honesty | The repro report's stated outcome, read against what its own artifacts (the Behavior-matches evidence) actually show. | The stated outcome matches the evidence exactly — an honest "could not reproduce, here's what I tried and saw instead" passes; a confident claim the artifacts don't back, or that targets the wrong behavior, fails. | required |
| Comms | The claim comment and repro comment text, read against the repo's own issue/PR template and contribution policy. | The comment states specifics about what was tried and observed (not boilerplate that could apply to any issue) and fills in the repo's required template fields. | required |
| Disclosure | The repo's contribution/AI-use policy (from the repo-facts block or repo docs), read against the claim and repro comment text. | If the repo's policy requires disclosing AI assistance (always, or under stated conditions that apply here), the comment states that AI was used and the extent of the assistance. If the policy has no such requirement, or its condition doesn't apply, this passes without a disclosure statement. Silence when disclosure is required is a fail, not an accept. | required |

## Verdict rule

Accept (ready to post) only if every required check passes. This rubric has
no preferred checks. `unclear` counts as a fail — proof I can't verify is
proof that isn't ready to post. Any required check failing or unclear holds
the package (reject).
