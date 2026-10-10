# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/119

**Branch**

feat/35-webhook-notifications

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run, 20 scored packages, `--save-run eval-run.txt`:
   `agreement: 19/20 scored items  (bar: 18/20: PASS)`, with
   `categories: clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`.
   The one disagreement was pkg-16 (see Package analysis).

That was my only run. It passed the bar and the category floor on the first try, so I made no
revisions and did no `--only` re-runs. The `eval-run.txt` I committed is this run. Its
fingerprints (`SKILL.md sha256:5d8bc17fca39c992`, `rubric.md sha256:58fe439c971a8f35`,
`procedure.md sha256:ad4da4fc72bb2501`, `evidence-guide.md sha256:8ebd128676f06f70`) match
the files I uploaded to `tools/pr-precheck/`.

**Package analysis**

`pkg-16` (minikube `image load`, category `clear-accept`). Gold: `accept`. My rubric:
`reject`, failing `Plan-fidelity` and `Description-matches-diff`.

The plan's scope says: "`DoLoadImages` returns an aggregated error when every target
machine failed", and it defers "the start-time loading path (multi-node, where a per-node
failure may genuinely be non-fatal)". The diff does exactly that. It adds
`if len(succeeded) == 0 { return fmt.Errorf("loading images: %w", ...) }` and leaves
partial failure non-fatal ("Partial success stays non-fatal for the multi-node start
path").

My grader's evidence line for the fail was: "DoLoadImages' new all-failed branch is
unconditional for every caller; plan required 'without changing the start path's
semantics' and only disclosed keeping partial/per-node start failures tolerant, not the
all-nodes-failed case this diff also changes." It reasoned that start-path callers also
call `DoLoadImages`, so a total failure at start now returns an error too, and that this
was an undisclosed change. It then failed `Description-matches-diff` on the same
inference, because the description says "the start-time image loading path keeps its
tolerant behavior."

Gold reads it as accept because the plan itself names the all-failed aggregated error as
the change. The deferred part is only per-node tolerance, which the diff keeps. My
`Plan-fidelity` pass condition says a hunk must be "something the plan or a deviation note
names", but it doesn't stop the grader from reasoning past the plan's own words into
downstream effects on other callers. It found a "silent" change that is really the plan's
named change seen from a different caller.

**Check rationale**

> | Repo-asks-met | The repo facts' PR-template sections and checklist items and the contributing guide's PR asks (live: `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and `scope.md` house rules), read against the title, description, and diff. | Every stated ask that applies to this change is met: required template sections present with real content (not left as the template's placeholder), the issue linked in the asked form (`Closes #N`, `fixes #N`), a title in the asked convention, a changelog or whatsnew entry in the diff when asked, tests in the diff when the repo asks for tests with behavior changes, and any gating ask honored (a repo "not accepting new features" gets only a bugfix; an ask to discuss first is followed). An ask that plainly does not apply to this change (docs for a change with no API/CLI surface, config docs when no config was added) passes without being mentioned. Process steps a package cannot show (signing a CLA, a vouch flow) are not graded. If the repo states no asks, this passes. | required |

This is my main standards-wall check, and that category has only 2 packages, so missing
both would break the category floor no matter how well I did elsewhere. I wrote it to list
the concrete kinds of ask I saw across the packages' repo-facts lines: template sections, an
issue-link form, title conventions, changelog/whatsnew entries, tests-with-changes, and
gating notes like Rich's "not accepting new features". I didn't want a vague "follows the
template".

The two exclusions are deliberate, and I'd rejected the stricter version. My first idea was
"every ask is met or explicitly marked n/a". But the clear-accept packages often skip
inapplicable checklist items without comment. calib-03 says "docs n/a", while others just
don't mention a doc ask for a change with no CLI surface. A strict reading would reject
good PRs for silence about things that don't apply. Process steps like a CLA or ghostty's
vouch flow can't be seen in a package at all, so under "unclear counts as fail" they would
fail every package from those repos. AI disclosure is a separate check (`AI-disclosure`),
not part of this one. That's because in Unit 2 my disclosure clause was buried at the end
of a combined Comms check, and the grader skipped it (pkg-20 in that unit). Both
standards-wall packages matched (`standards-wall 2/2`).

**Trade-offs**

`Plan-fidelity` reads both directions strictly: every hunk must trace to the plan, and
every plan step must appear in the diff or be disclosed. It costs me pkg-16. The grader
used that strictness to infer an undisclosed side effect from a change the plan itself
names. I accepted this miss rather than loosening the check, for two reasons. First, the
same strictness is what catches all four `silent-drift` packages (`silent-drift 4/4`),
where the diff really does more or less than the plan. Second, any loosening, such as
"grade only what the plan's words name, not inferred effects on other callers", could flip
a silent-drift package that agreed. Checking that would have needed `--only pkg-16` plus
silent-drift canaries (pkg-03, pkg-06, pkg-09, pkg-17). I didn't run it, because the full
run already passed at 19/20 with every category matched, and a change could only risk the
floor I already had. If I revise this, the fix is one sentence in the pass condition
("a hunk that does what a plan step literally names passes, even if it affects other
callers"), re-checked against those four canaries before a confirming full run.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
