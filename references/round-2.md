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

Before spawning anything, read the `CRUX:` line. It must be exactly one decision-relevant question that reasoning can resolve. If it is compound (joined by "and" or "or") or plainly depends on missing external evidence, send it back to the chairman once with that note. If the second attempt still fails the check, treat the gate as INSUFFICIENT and state the specific missing information or evidence in the verdict.

## What Round 2 advisors receive

New sub-agents, not the Round 1 advisors. They carry no memory of Round 1.

**They receive:**
- The framed question.
- The crux.
- The Round 1 Findings Handoff produced by the chairman.

The handoff contains:
- Supported findings.
- Strong arguments.
- Weak or rejected arguments.
- Disagreements.
- Critical assumptions.
- Evidence quality.
- Blind spots.
- Open questions.

**They do not receive:**
- The Round 1 recommendation or verdict.
- The chairman's interim lean.
- The Round 1 confidence.
- Any instruction to defend or overturn the Round 1 conclusion.
- The full Round 1 advisor or reviewer transcript.

Why: Round 2 should inherit knowledge without inheriting judgment. The findings
prevent unnecessary repetition, while withholding the conclusion, confidence,
and lean forces the second round to independently evaluate what actually holds.

**The Outsider** receives only the raw user question (trigger phrase removed)
plus the crux. It does not receive the Round 1 Findings Handoff, since that would
give it the context it is deliberately meant to lack. If the crux cannot be
understood from the raw question alone, restate it in plain language for the
Outsider only, without adding any Round 1 reasoning or conclusion.

## Round 2 advisor prompts

Use the prompt templates in `advisors.md` with these changes:

**Standard advisors:** after the framed question, add:

The first round produced a structured set of findings about this problem.

ROUND 1 FINDINGS:
[Round 1 Findings Handoff]

The remaining crux is:

CRUX: [crux]

Use the findings to avoid repeating reasoning that is already well supported.
However, treat every finding as something to evaluate, not as established truth.
Pay particular attention to weak arguments, disagreements, assumptions, and
evidence gaps.

Your job is to determine what the reasoning actually supports about the crux.
Don't simply agree with or overturn the first round. If the crux is badly framed,
say so. If it requires information the council does not have, identify exactly
what is missing and why reasoning cannot supply it.

## Round 2 peer review

Follow `peer-review.md` with a new random shuffle. Keep all of its rules, including anonymization, reviewer independence, and the reviewer output requirements. Make only these two changes:

- Add the crux to the reviewer prompt, after the framed question: "The council is investigating this specific crux: [crux]."
- Reword question 3 to: "What did all five miss about the crux?"

Everything else, including the anonymization and the question 1 and 2 structure, is unchanged.

## Round 2 chairman

Follow `chairman.md`, with these differences:

- **Gates available:** `FINAL` or `INSUFFICIENT` only. `ROUND_2` is not available.
- **Round 1 findings are inherited, not accepted** The chairman receives the structured Round 1 Findings Handoff and evaluates those findings against the Round 2 reasoning. A finding may be retained when Round 2 supports it, revised when Round 2 exposes a weakness, or rejected when Round 2 contradicts it.

The Round 1 verdict, confidence, and interim lean are not treated as evidence and must not influence the final judgment merely because they came first.
- **Inputs:** The framed question, the crux, the Round 1 Findings Handoff, all five Round 2 advisor responses, all five Round 2 reviews, and the anonymization mapping.

The Round 1 advisor and reviewer responses may remain in the transcript for auditability, but are not required for Round 2 synthesis.

- **Add this section** to the verdict body, directly after the Verdict:

```
## What Round 2 Changed
[Confirmed | Revised | Unresolved]: one or two sentences.

Confirmed: Round 2 independently supported the relevant Round 1 findings.

Revised: Round 2 exposed a weakness, contradiction, or new consideration that
changed one or more Round 1 findings or the resulting decision.

Unresolved: Round 2 did not settle the crux.
```

## If uncertainty remains

Round 2 may not settle the crux, and that is an acceptable outcome. The chairman finalizes anyway, and the remaining uncertainty goes into the verdict:

- If the answer now splits on a checkable assumption, finalize as **Conditional Recommendation**, with a cheap way to check it.
- If the crux turned out to depend on evidence the council can't generate, finalize as **Insufficient Information**, naming the missing information and the next action.
- Do not run a third round. If the user wants to continue, they start a new council with new evidence.
