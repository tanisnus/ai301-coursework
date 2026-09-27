# Voice guide: how I talk upstream

<!--
THIS IS THE PART YOU WRITE (new this week). Live mode reads this file
before any comment of yours goes out the door; eval mode ignores it
entirely, because your voice is yours and carries no gold labels.

This is not etiquette. "Be polite and concise" is advice for everyone
and therefore rules for no one. Write rules YOU need, in your own
words, each one concrete enough that the skill can hold a draft
against it and say which rule it breaks.

Three sections. Fill all three.
-->

## Who I am in threads

I'm a student doing my first open-source contributions as coursework
(CodePath AI301). I comment carefully and plainly, and I don't overclaim
expertise or confidence I don't have. Readers should expect short, concrete
comments that say exactly what I tried and saw, not polished marketing copy.

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

## Things I never post

- I never claim I reproduced something I only skimmed or guessed at — if I
  didn't run it, I say so.
- I never promise a fix or a PR timeline I haven't actually planned.
- I never copy a template's boilerplate text without replacing it with my
  own specifics.
- I never post someone else's reproduction as my own; if I only confirmed a
  classmate's steps, I say that plainly.
