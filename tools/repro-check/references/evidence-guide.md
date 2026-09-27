# Evidence guide: where proof lives in a reproduction package

<!--
THIS IS THE PART YOU WRITE (new this week: Unit 1 handed you this file
finished; the scaffolding fades). The skill uses this guide as its map:
for every kind of proof a rubric check names, this file says WHERE to
find it in a package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repo-facts block, the
  claim comment, the repro report and its parts). In live mode (where
  on GitHub or in the draft: the issue thread, the repo's docs, the
  student's draft comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the versions named match what the
  issue targets, or the difference is called out") over adjectives
  ("environment is thorough").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute; the rubric swap showed you what that feels
like. Write the map you wish your grader had.
-->

## Environment

Where it lives: in an eval bundle, the repo-facts block's stated target
version/OS and the candidate repro report's own environment section. Live,
the draft repro comment's environment section, checked against the issue
thread or repo docs for what version the issue targets.

What good looks like: a concrete tool version and OS/platform are named,
not "latest" or "my machine." If the reproduction's environment differs
from the issue's stated target, the report says so explicitly rather than
staying silent about the gap.

## Steps

Where it lives: the repro report's steps or commands section.

What good looks like: someone with no other context could start from the
stated initial state and reach the same trigger using only what's written —
no missing setup step, no "click around until it happens."

## Behavior shown

Where it lives: the repro report's output excerpt, log, or screenshot
section.

What good looks like: the excerpt contains the same failure text or symptom
the issue describes (or a clearly equivalent one) — not a different failure
that merely looks similar at a glance. When the report instead states it
could not reproduce the issue, this still counts as showing the behavior
question honestly answered if the excerpt documents a genuine, specific
attempt and names exactly what happened instead (which input, which output,
how it differs) — a cannot-reproduce is not automatically "no behavior
shown"; a vague or missing attempt is.

## Honesty

Where it lives: the repro report's stated conclusion or summary line, read
against its own Behavior shown evidence.

What good looks like: the stated outcome is exactly what the artifacts
support. An honest "I could not reproduce this — here's what I tried and saw
instead" is as valid a pass as a confirmed reproduction. A confident claim of
reproduction that the artifacts don't actually back, or that targets the
wrong behavior, does not pass.

## Comms

Where it lives: the claim comment and repro comment text, read against the
repo-facts block's stated issue/PR template and contribution policy (eval
mode); the repo's actual docs and templates (live mode).

What good looks like: the comment names specifics — what was tried, what was
seen — rather than boilerplate generic enough to paste onto any issue, and
fills in the fields the repo's template requires.

## Disclosure

Where it lives: the repo-facts block's stated AI-use/contribution policy
(eval mode) or the repo's CONTRIBUTING/AI-policy docs (live mode), read
against whether the claim or repro comment states that AI was used and how.
Read the policy's own wording carefully — some repos require disclosure
always, some only under a stated condition, and some have no requirement at
all (or explicitly welcome undisclosed use).

What good looks like: when the policy requires disclosure (unconditionally,
or its stated condition is met here), the comment says so plainly — naming
the tool and the extent of the assistance — not folded silently into
"AI-assisted" boilerplate. When the policy has no such requirement, or its
condition doesn't apply, no disclosure statement is needed and its absence
is not a mark against the package. Treat quiet omission under a policy that
does require it as the failure this check exists to catch — it is the
easiest thing for a package to get right by accident and easiest to miss by
skimming.
