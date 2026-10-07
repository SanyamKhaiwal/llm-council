# Round 2

Round 2 is a controlled, one-time second reasoning pass aimed at a single question the first round couldn't settle. It is not "run the same council again." The gate that decides whether Round 2 happens is defined in `chairman.md`. This file covers only what happens after the chairman has returned `GATE: ROUND_2`.

## Contents

- When Round 2 runs and when it doesn't
- Sanity-check the crux
- What Round 2 advisors receive
- Round 2 advisor prompts
- Round 2 peer review
- Round 2 chairman
- If uncertainty remains

## When Round 2 runs and when it doesn't

Round 2 runs only when the chairman returned `GATE: ROUND_2` with a `CRUX:` line.

Round 2 does not run when:

- The gate was `FINAL` or `INSUFFICIENT`.
- Round 2 has already run in this council. It is the final reasoning pass, at most once.
- The crux is a missing fact that reasoning can't supply. Another round of the same model produces more opinions, not more evidence.

## Sanity-check the crux

Before spawning anything, read the `CRUX:` line. It must be exactly one decision-relevant question that reasoning can resolve. If it is compound (joined by "and" or "or") or plainly depends on a fact nobody has, send it back to the chairman once with that note. If the second attempt still fails the check, treat the gate as `INSUFFICIENT`, with the crux as the missing information.

## What Round 2 advisors receive

New sub-agents, not the Round 1 advisors. They carry no memory of Round 1.

**They receive:**
- The framed question (the same one Round 1 used).
- The crux.
- The chairman's "Open Questions for Round 2" section: the unresolved disagreements and weak assumptions, phrased as open questions.

**They do not receive:**
- The Round 1 recommendation or verdict.
- The chairman's interim lean.
- Round 1 advisor responses or reviews.

Why: the point of Round 2 is to investigate the crux with fresh eyes. Showing the previous conclusion, even in passing, turns the round into a search for confirmation.

**The Outsider** receives the raw user question (trigger phrase removed) plus the crux, and nothing else. It does not get the open-questions section, since that would hand it the context it is meant to lack. If the crux can't be understood without insider context, restate it in plain language for the Outsider only, without adding the answer.

## Round 2 advisor prompts

Use the prompt templates in `advisors.md` with these changes:

**Standard advisors:** after the framed question, add:

```
The first round of analysis left one question unresolved:

CRUX: [crux]

Open questions the earlier analysis did not settle:
[open questions section]

Your job is to address this crux directly from your perspective. Don't summarize
the whole problem again. If your lens suggests the crux is badly framed, say so.
If it can only be resolved with information the council doesn't have, say exactly
what information and why reasoning can't supply it.
```

**Outsider:** after the raw question, add:

```
One specific question needs a fresh read:

CRUX: [crux]

Answer it as a stranger would, from what is in front of you.
```

The shared rules (work alone, flag assumptions, don't invent facts, 150-300 words) are unchanged.

## Round 2 peer review

Follow `peer-review.md` with a new random shuffle, and two changes:

- Add the crux to the reviewer prompt, after the framed question: "The council is investigating this specific crux: [crux]."
- Reword question 3 to: "What did all five miss about the crux?"

Everything else, including the anonymization and the question 1 and 2 structure, is unchanged.

## Round 2 chairman

Follow `chairman.md`, with these differences:

- **Gates available:** `FINAL` or `INSUFFICIENT` only. `ROUND_2` is not available.
- **Round 1 is a hypothesis.** The chairman receives the Round 1 chairman output (the verdict or interim lean, the assumptions, the disagreements) and treats it as a claim to test against Round 2, not a conclusion to confirm. Don't carry over Round 1 confidence automatically.
- **Inputs:** Round 1 chairman output, the crux, all five Round 2 advisor responses, all five Round 2 reviews, and the anonymization mapping. Round 1 advisor responses stay in the transcript. The chairman doesn't need them.
- **Add this section** to the verdict body, directly after the Verdict:

```
## What Round 2 Changed
[Confirmed | Revised | Unresolved]: one or two sentences. Confirmed: Round 2
supported the Round 1 lean. Revised: Round 2 changed the answer, and why.
Unresolved: Round 2 did not settle the crux.
```

## If uncertainty remains

Round 2 may not settle the crux, and that is an acceptable outcome. The chairman finalizes anyway, and the remaining uncertainty goes into the verdict:

- If the answer now splits on a checkable assumption, finalize as **Conditional Recommendation**, with a cheap way to check it.
- If the crux turned out to depend on evidence the council can't generate, finalize as **Insufficient Information**, naming the missing information and the next action.
- Do not run a third round. If the user wants to continue, they start a new council with new evidence.
