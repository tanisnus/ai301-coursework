# Contribution: Ability to Disable/Choose No Theme

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Tanis Sarbatananda
**GitHub Username:** tanisnus
**Issue:** https://github.com/code-charity/youtube/issues/4329
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Turned my issue grader into a scout that searches GitHub, and fixed the checks a real scan exposed**

I upgraded my Unit 1 grader into issue-scout by writing six `gh search issues` queries from my fit profile (TypeScript, Swift, UI, LLM, chat, GitHub Actions) and eight ranking rules, which narrowed 600 results to five graded candidates. The first scan accepted mailair#18, a task that only a repo admin could do (deleting an Actions secret), and all four accepted repos had 0–1 stars. So I added a "can land as a PR from a non-maintainer" condition to my newcomer-scope check and a new `repo_adoption` check, and fixed two ranking rules that weren't doing what I intended (the Swift date window and the multi-match order). When I graded my Issue Selection site picks, the scout rejected five of six for real reasons (two already had open PRs from other contributors, two were in a repo with no commits in 92 days, and one was design-only work with no code to change), but it also showed a gap I haven't fixed yet: it treats any closed linked PR as "unclaimed" without checking why it was closed.

**Unit 6: Installed a voice-guard hook over my upstream posts, and wrote a local repo-memory file for my fork**

I consolidated my voice guide into one canonical file, added an Upstream section with rules for the claims, questions, and pings I post, and installed a PreToolUse hook that runs the voice pass on every `gh issue comment` or PR body I send through Claude Code. It fired for real on my claim to code-charity/youtube#4329: the wiring test was stopped as expected, one of my own commands was stopped because I passed the body path as a shell variable (`--body-file "$DRAFT"`, which the hook reads literally and cannot resolve), and once I re-ran it with the literal path the voice pass passed and the comment posted, logging a clean entry in guard-log.md. I also wrote a `CLAUDE.local.md` for my youtube fork: one scoped orientation showed the theming is applied in two layers (an `it-theme` CSS attribute written from storage, plus side effects in `setTheme()`), so I distilled that into the file and kept it out of my PR with `.git/info/exclude` instead of re-learning the subsystem every session.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I found this issue while grading candidate issues with my scout (issue-scout).
My Unit 5 scan built six `gh search issues` queries from my fit profile and
narrowed about 600 results down to a handful of graded candidates; alongside
the scan I graded picks directly. After the scope patch I graded two:
code-charity/youtube#4329 and visjs/vis-timeline#1231. Both came back accept,
difficulty moderate.



 I decided to choose youtube. code-charity/youtube#4329
asks for an option to turn off the extension's built-in theming (or a "None"
theme) so other theming methods, like Stylus, are not overridden. I re-graded
it this week under my patched rubric and it came back accept, with every
required check passing and a difficulty note of moderate. It fits my profile:
it is JavaScript and it is a UI/UX feature in a browser extension, which is the
web-development and UI/UX work I want to get better at. The repo is active
(commits on the day I graded it), it is labelled good first issue and
up-for-grabs, and its CONTRIBUTING is silent on AI, so I can contribute here
honestly.





What I hope to learn is how to find my way around a large, unfamiliar codebase:
the issue names the feature area (the theming section of the options) but not
the file, so part of the work is tracing how the extension applies its theme
today. Honest read of my scout: the reason it could accept this issue at all is
the scope change I made this week. My old scope check rejected any issue with
an open edge, which is most real issues in the wild, so I replaced it with a
`scope-feasible` floor that only rejects issues that are not a single piece of
work, and moved the scope detail into a difficulty note. I kept my own
admin-access reject from an earlier scan, so an issue only a repo admin could
land still fails. The scout still has a gap I logged but have not fixed: it
treats any closed linked PR as "unclaimed" without reading why it closed, and
this issue has a closed PR (#4330), so I read that thread myself rather than
trusting the check.

Claim comment: https://github.com/code-charity/youtube/issues/4329#issuecomment-6399821904

**Toolkit**

- Tool: issue-scout (my search strategy and my rubric), run in direct
mode to grade this issue (and a second candidate) against my rubric.
- What it produced: an accept verdict with every required check passing
and a difficulty note of moderate; the read-out is saved in
scan-output.md (Round 3).
- Difficulties: my old `scoped_for_newcomer` check would have rejected
real wild issues for having an open edge, and the `unclaimed` check
passes any closed linked PR without checking why it closed (this issue
has a closed PR, #4330).
- What changed: I applied the `scope-feasible` patch (replaced
`scoped_for_newcomer`, kept my admin-access floor, added difficulty
notes); the closed-PR gap is logged in scan-notes.md but not yet
patched, so I read PR #4330's thread by hand.

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

macOS 26.4 (Darwin 25.4.0), Google Chrome 156.0.8078.47. I cloned my fork
(tanisnus/youtube, branch `feature/disable-theme`, commit `6d85ec53`, the
ImprovedTube extension at manifest v4) and loaded it unpacked via
chrome://extensions -> Developer mode -> Load unpacked -> the clone folder.
This is a source-level reproduction read against that commit and confirmed in
the extension's own menu; the clone builds nothing for Chrome, so no setup
challenges came up. (Local Node is v26.7.0 while the repo's CI pins Node 20,
but that only affects the test run, not this runtime-theming reproduction.)

**Steps to Reproduce**

1. List the theme options the extension offers: read the `group: 'theme'` radio

   options in `menu/skeleton-parts/themes.js` (or open the loaded extension's
    menu and go to the Themes section).
2. Look for an option that turns the extension's theming off entirely.
3. Check how a chosen theme is applied: read the `html[it-theme]...` rules in

   `js&css/extension/www.youtube.com/styles.css` and `ImprovedTube.setTheme()`
    in `js&css/web-accessible/www.youtube.com/themes.js`.
4. Observed result: the options are `light, dark, custom, black, plain, sunset,

   night, dawn, desert`with no "None"/disable option; any non-default theme  applies`!important`CSS that overrides other styling; and`setTheme()` has
    no branch that removes the extension's theming.

**Reproduction Evidence**

- **Commit showing reproduction:** none (observational / source-level; no fix committed).
- **Screenshots/logs:** code excerpts at commit `6d85ec53`:
`menu/skeleton-parts/themes.js` theme values `light` (L58), `dark` (L73),
`custom` (L85), `black` (L118), `plain` (L128), `sunset` (L138), `night`
(L148), `dawn` (L158), `desert` (L168) — no disable/None;
`js&css/extension/www.youtube.com/styles.css` L744–748
`html[it-theme]:not([it-theme=default]) ... { ... !important; }` (plus L751–757
forcing link/text colors); `js&css/web-accessible/www.youtube.com/themes.js`
L101–128 `setTheme()` has cases for each named theme and `default` but none to
disable theming. (A Themes-menu screenshot can be added once the extension is
loaded; it shows the same option list.)
- **My findings:** the issue is real on commit `6d85ec53`. There is no user
option to disable the extension's theming, and selected themes apply
`!important` overrides, which is the behavior the reporter describes. The fix
will need to neutralize both layers: make a new option produce no `it-theme`
CSS match and have `setTheme()` tear down its prior side effects (dark
attribute, custom-color style, #cinematics, the YT `f6` PREF cookie). Honest
limit: I verified this from the source and the menu's option list, not yet
from a live Stylus side-by-side.

**Toolkit**

- Tool: repro-check, run on my claim-draft.md and repro-draft.md together
against the issue (full-package mode).
- What it produced: an accept verdict with all seven required checks passing
(Env-recorded, Input-matches, Behavior-matches, Steps-complete, Honesty,
Comms, Disclosure), and a clean voice-guide pass.
- Difficulties: none met; the one thing it correctly did not let me overclaim
was the live behavior, so I kept the Stylus side-by-side out of the findings
as an honest limit rather than asserting it.
- What changed: nothing in the rubric this phase; the report passed on the
first draft, so the work was writing it to the evidence I actually had.

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
