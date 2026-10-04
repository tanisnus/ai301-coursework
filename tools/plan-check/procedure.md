# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

1. Read the repo-facts block and the issue (title, body, labels) first.
   Note the specific failing behavior the issue describes and the repo's
   stated contribution/AI-use policy. Do not read the plan yet.
2. Read the thread highlights (or, live, the actual issue thread) next,
   before the plan. Note any explicit maintainer diagnosis, chosen
   approach, or rejected approach, and note if the thread contains none of
   these. Reading this before the plan matters: forming an independent
   read of "what did the maintainer already say" first means the plan's
   own framing can't quietly stand in for the thread when they conflict —
   this is the step the Thread-aware check depends on.
3. Read the repro evidence next, still before the plan. Note the exact
   behavior shown, and separately note any control run, debug/verbose
   output, or "actual vs. expected" line, and what each one rules in or
   out. Reading this before the plan for the same reason as step 2: an
   independent read of what the evidence actually proves, formed before
   seeing what the plan claims it proves, is what the Grounded-cause check
   depends on.
4. Read the candidate plan last: Diagnosis, then Scope, Files, Approach,
   Test plan, and any stated risks/open questions, in that order. Then
   read the candidate plan comment.
5. In live mode only, also read `scope.md` (before anything above) and
   `voice-guide.md` (after the plan comment) per SKILL.md's instructions.

## Evidence gathering

For each rubric check, pull its fact from the noted reading above rather
than re-scanning the package:

- **Grounded-cause**: from step 4, the plan's stated cause (the exact
  sentence or clause naming it). From step 3, the specific control/debug
  fact that either supports or rules out that cause. Record both as a
  pair; a check with no such control to compare against still gets
  graded against whatever repro evidence exists.
- **Bounded-scope**: from step 4, the plan's Scope line and its full list
  of files/approach items. From step 1, the single behavior the issue
  names. Record any file or step in the plan that is not required to
  reach that named behavior.
- **Executable**: from step 4, whether every approach step names a file,
  location, or concrete action, or whether any step defers a real
  decision ("whichever is easier," "not sure which layer") to build time.
- **Decisive-test**: from step 4's Test plan, the exact outcome it names.
  From step 3, the repro evidence's own steps/artifacts, to check whether
  the named outcome is actually tied to the fix (would come out
  differently if the fix were wrong) or is generic/subjective.
- **Honest-uncertainty**: from step 4, any risk/unknown/open-question
  language, plus a re-read of the Diagnosis/Test plan sections for claims
  that assert something as fact that the plan's own evidence doesn't
  establish (an unmeasured cost stated as negligible, a platform behavior
  assumed without having checked it).
- **Thread-aware**: from step 2, the maintainer direction noted (or its
  absence). From step 4's plan comment, whether that direction is named
  and engaged.
- **Disclosure**: from step 1, the exact policy wording. From step 4's
  plan comment, whether it states AI use and how much, when the policy's
  condition applies here.

## Check execution

Grade the checks in the rubric's table order: Grounded-cause,
Bounded-scope, Executable, Decisive-test, Honest-uncertainty,
Thread-aware, Disclosure. Grade every check from the gathered evidence
above without re-reading the whole package; only re-read a specific
section if a check's gathered note is ambiguous about what it says.

When a check's evidence family is genuinely absent from the package (no
stated risks at all, no thread comments at all), do not default to
`unclear`: apply the rubric's stated pass condition to that absence. Most
of this rubric's checks pass by default on absence of the *bad* signal
(no thread direction to ignore, no risk section because the plan states
nothing that needs qualifying) — grade `unclear` only when the package
gives no way to tell either way (for example, a repro evidence block too
thin to confirm or rule out the stated cause).

Grade Grounded-cause first because when it fails, the remaining checks
are graded from a plan built on a cause the evidence doesn't support —
still grade them honestly from what's written, but note in the summary
that a later check's "pass" is conditional on a diagnosis that already
failed.

## Verdict assembly

Apply the rubric's verdict rule exactly: accept only if all seven required
checks pass; any required check graded `fail` or `unclear` produces
reject. There are no preferred checks in this rubric, so no check's
result is ever discarded.

In the output JSON, the `evidence` field for a `fail` or `unclear` check
must quote the specific fact gathered above that decided it (the control
run that rules out the cause, the placeholder phrase that fails
Executable, the policy line disclosure was silent against) — not a
restatement of the check's name. For a `pass`, the evidence field names
the fact that satisfied the condition. Produce the same verdict from the
same set of check grades every time; the verdict rule has no
discretion left in it once the checks are graded.
