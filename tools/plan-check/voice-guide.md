# Voice guide: how I talk upstream

<!--
THIS IS A CARRY-OVER SLOT, not a new hole. You wrote this guide in
week 2; paste your filled week-2 voice-guide.md here, whole. It is not
re-authored and it is not graded as new work this week.

Then reread it with the plan comment in mind. Your claim and repro
comments promised and reported; a plan comment commits you to an
approach in front of the people who maintain the code. If your rules
do not cover that register (for example: how you state an approach you
are not certain of, or how you respond when a maintainer already
suggested a direction), extend the guide with what it needs. Extending
is allowed and encouraged; starting over is not required.

Live mode reads this file before your plan comment goes out and
reports any rule your draft breaks. Eval mode ignores it entirely,
because your voice is yours and carries no gold labels.
-->

## Who I am in threads

I'm a student doing my first open-source contributions as coursework
(CodePath AI301). I comment carefully and plainly, and I don't overclaim
expertise or confidence I don't have. Readers should expect short, concrete
comments that say exactly what I tried and saw, not polished marketing copy.
A plan comment is a step further than a repro comment: I'm committing to an
approach in front of the people who maintain the code, not just reporting
what happened. I hold that commitment to the same plainness — say what I'll
do and why, not what I hope it looks like once it's done.

## Rules I write by

### Rule: No decorative dashes

Join clauses with commas or periods, not em-dashes. A string of em-dashes
reads like it was written to sound impressive, not to report a fact.

- Wrong: "Ran the script — got a crash — here's the trace."
- Right: "I ran the script and it crashed. Here's the trace."

### Rule: No fancy words for plain ones

Use the plain word for the thing. Don't reach for a bigger synonym to sound
more capable than the sentence needs to.

- Wrong: "I meticulously scrutinized the codebase and ascertained the root
  cause."
- Right: "I looked through the code and found the cause."

### Rule: No manufactured enthusiasm

State findings flatly. Don't add excitement the content hasn't earned.

- Wrong: "Great news! I was able to successfully reproduce this issue!"
- Right: "I reproduced the issue. Steps and output are below."

### Rule: No arrow-chain formatting

Write steps as a numbered or plain list. Don't chain steps together with →
glyphs.

- Wrong: "Open file → run script → see crash"
- Right: "1. Open the file. 2. Run the script. 3. It crashes."

### Rule: Keep sentences short and plain

One idea per sentence, plain grammar, no nested clauses. If a sentence needs
a re-read to parse, split it.

- Wrong: "Given that the reproduction, which was performed on the latest
  commit, yielded output that, upon inspection, appeared to diverge from the
  expected behavior described in the issue, it seems plausible that a
  regression was introduced."
- Right: "I reproduced this on the latest commit. The output doesn't match
  what the issue describes. This looks like a regression."

### Rule: State an approach as a plan, not a done deal (new this week)

A plan comment describes what I intend to do, not what already happened.
Say "I plan to" or "my approach is," not language that implies the fix is
already written or verified when it isn't.

- Wrong: "This fixes the null pointer in the parser."
- Right: "I plan to fix the null pointer in the parser by checking for an
  empty token list before dereferencing it."

### Rule: Name the maintainer's direction before adding mine (new this week)

When a maintainer already proposed, confirmed, or rejected an approach in
the thread, say so explicitly before stating my own plan, even if my plan
follows theirs exactly. Silently adopting their diagnosis without crediting
where it came from reads as if I found it myself.

- Wrong: "The generation counter approach handles this cleanly."
- Right: "My plan follows the approach proposed here: a generation counter
  bumped on capacity changes."

### Rule: Say what's unmeasured, not just what's unknown (new this week)

If a cost, a benchmark, or a platform behavior hasn't actually been
checked, say that plainly instead of asserting it will be fine.

- Wrong: "The extra comparison won't affect performance."
- Right: "I haven't measured the per-print cost of the comparison yet; if
  it shows up in the print benchmark I'll narrow where it runs."

## Things I never post

- I never claim I reproduced something I only skimmed or guessed at — if I
  didn't run it, I say so.
- I never promise a fix or a PR timeline I haven't actually planned.
- I never copy a template's boilerplate text without replacing it with my
  own specifics.
- I never post someone else's reproduction as my own; if I only confirmed a
  classmate's steps, I say that plainly.
- I never state a plan's outcome as settled before I've built it — a plan
  commits to an approach, not to a result I haven't produced yet.
- I never skip an AI-use disclosure a repo's policy requires, even when the
  policy is easy to miss on a skim.
