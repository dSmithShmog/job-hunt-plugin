# Voice extraction guide

How to turn real writing samples into a `voice.md` that keeps drafts sounding
like the user instead of like a cover-letter generator.

## Read for these, in order

1. **Sentence mechanics.** Average sentence length and its variance. Fragment
   tolerance. Contractions or not. Punctuation habits: dashes, semicolons,
   parentheticals, exclamation points. Record what they DO, not what looks good.
2. **Vocabulary temperature.** Plain verbs vs corporate verbs. Do they say
   "built" or "architected"? "ran" or "orchestrated"? Collect 5-10 verbs they
   actually use for their own work.
3. **Recurring constructions.** Any opener, closer, or framing that appears in
   2+ samples is a candidate signature move. Quote it exactly; ask the user
   "is this deliberate — should new drafts reuse it?"
4. **Humor and warmth.** Where does levity show up, how often, and what flavor
   (dry, self-deprecating, none)? Count per document; the count becomes a
   budget ("one wry beat per letter, max" style).
5. **What they never do.** Absences are rules too: no buzzwords, no
   exclamation points, never opens with "I am writing to..." — record as bans.
6. **Their edits, if you have before/after pairs.** The single best signal.
   Every deletion implies a ban; every rewrite implies a preference. Quote the
   dead line in Review corpses with the inferred reason.

## Universal AI-tells to pre-seed into "Banned and flagged"

These read as machine-written regardless of the user's style; seed them, then
let the user veto:

- Correlative constructions: "not X, but Y", "It isn't X. It's Y."
- Aphoristic balance closes: "...And that is the whole job."
- Significance-reaching abstractions that inflate a concrete fact into a
  statement about the world.
- Causal chains with missing links (small cause, grand effect, no mechanism).
- "passionate about", "results-driven", "synergize", "utilize", "delve".
- Hedged closers ("I'd love the opportunity to...", "I hope to hear...").

## Interview questions (when samples are thin)

- "Paste two sentences you've written that sound most like you."
- "What words in other people's cover letters make you cringe?"
- "Formal or conversational when writing to a stranger about a job?"
- "How do you refer to your own work: built, led, ran, shipped, designed?"
- "Any punctuation you refuse to use?"
- "How long should a cover letter be, in your opinion?"

Mark interview-derived rules `(interview)` — they're weaker evidence than
samples and should yield when finished approved examples accumulate in
`examples/`.

## Calibration test

Before finalizing, write one 3-sentence paragraph in the extracted voice on a
neutral topic and show it next to a sample. Ask: "does this sound like you?"
Iterate until yes. Their corrections go straight into voice.md.
