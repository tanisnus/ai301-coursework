# Evidence guide: where evidence lives in a plan package

<!--
THIS IS THE PART YOU WRITE (second week running: the judgment files
stay in your hands). The skill uses this guide as its map: for every
kind of evidence a rubric check names, this file says WHERE to find it
in a plan package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repro-evidence block,
  the candidate plan's scope statement or test plan, the plan comment,
  the repo-facts block). In live mode (where on GitHub or in the
  draft: the issue thread, the student's posted repro comment, the
  repo's docs, the draft plan and comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the stated cause cites behavior the
  repro evidence actually shows") over adjectives ("diagnosis is
  solid").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts twice: your
procedure.md tells the skill WHEN to gather each family, and this
guide tells it WHERE. Write the map you wish your executor had.
-->

## Diagnosis and grounding

Where it lives: in an eval bundle, the candidate plan's Diagnosis section,
read against the Repro evidence block's steps, control runs, and any
`--debug`/verbose output. Live, the draft plan's stated cause, read against
the student's own posted repro comment (or the house repro pack) on the
issue.

What good looks like: the stated cause explains the specific failing
behavior the repro evidence shows, including any control run. A control
that holds one variable fixed and changes the outcome (or an unchanged
variable that still fails) is evidence *against* whatever cause that
variable was supposed to explain — a diagnosis the control rules out does
not get to stand on confidence alone. A diagnosis that never engages a
control or debug line the repro evidence includes is exactly the "ignores
the evidence" failure this check exists to catch.

## Scope

Where it lives: the plan's Scope and Files (or Approach) sections, read
against the specific behavior the issue names and the repro evidence
targets.

What good looks like: one change, addressed at the diagnosed cause, with
an explicit not-in-scope line. Files/areas named are the ones the fix
actually touches — not every file "in the area." A plan that defers
related work it noticed, and says why, is bounded; a plan whose approach
quietly grows into a rewrite, a migration, a new abstraction, or a second
feature is not, however well-reasoned the extra work sounds on its own.

## Executability

Where it lives: the plan's Files and Approach sections.

What good looks like: named files or code locations, plus an ordered list
of concrete steps, such that a stranger — a groupmate, a maintainer, the
skill itself — could start the first step without asking the author what
they meant. Words that push a real decision to build time ("wherever it
ends up," "whichever is easier," "not sure which layer," "investigate and
see") are the tell; a plan with one of those in place of a decision fails
here even if everything else about it reads well.

## Test plan

Where it lives: the plan's Test plan section, read against the Repro
evidence block's steps and artifacts.

What good looks like: a named, observable check tied to the fix itself —
re-running the repro's exact steps with a stated expected result, a
specific assertion, a specific exit code or output line — something that
would come out differently if the fix were wrong. "Should feel fast,"
"shouldn't feel broken," or "run the full test suite" with no outcome
named for the change itself is not decisive, even when it's true that the
suite would eventually catch a real regression.

## Honesty

Where it lives: the plan's stated risks, unknowns, deviations, or open
questions, read against its own diagnosis, scope, and test-plan sections.

What good looks like: a genuine unknown the plan is still resting on (an
unmeasured cost, an unconfirmed platform behavior, a question left for
review) is named as an unknown, in the plan's own words — not folded
silently into a confident sentence elsewhere. This is not the same
failure as Grounded-cause: a plan can be right about its cause and still
overclaim what it hasn't checked (a performance number it hasn't
measured, a platform it hasn't tested), and that overclaim is what this
check catches. Mid-build, this is also where an honest deviation gets
recorded: a `plan.md` that states what changed and why, in the student's
own words, is the honest version of this same standard.

## Comms

Where it lives: the plan comment text, read against two separate sources —
the thread highlights (eval) or the live issue thread (live mode) for any
explicit maintainer direction, and the repo-facts block's stated
contribution policy (eval) or the repo's actual CONTRIBUTING/AI-policy
docs (live mode) for disclosure requirements.

What good looks like, thread-aware: when a maintainer has already named a
cause, proposed an approach, or rejected one, the comment says so and
either follows it or gives a specific reason for departing from it. A
comment that reads as if the thread were empty — proposing a workaround
when the maintainer already isolated the real culprit, for instance — is
the failure this half of the check exists to catch, even when the
workaround itself is reasonable engineering.

What good looks like, disclosure: read the policy's exact wording. Some
repos require disclosing AI assistance unconditionally, some only under a
stated condition, some have no requirement at all. When disclosure is
required and its condition is met, the comment states plainly that AI was
used and how much — not folded silently into generic boilerplate. When no
requirement applies, no disclosure statement is needed and its absence is
not a mark against the comment. An otherwise excellent plan comment that
quietly skips a disclosure the policy requires still fails this half of
the check.
