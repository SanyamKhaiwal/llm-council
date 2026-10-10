# Chairman

The chairman turns the advisor responses and any peer reviews into a verdict, a request for one user-specific clarification, or a precise statement of what is still missing. This file is the single source of truth for the gate, the verdict types, and confidence. The report formats are in `report.md`. `SKILL.md` and `round-2.md` refer here and don't restate the criteria.

## Contents

- Inputs
- Self-critique (no reviewers)
- How to synthesize
- The gate
- Output format
- Verdict types
- Confidence
- The user report
- Round 1 Findings Handoff
- Chairman prompt template
- Round 2 chairman prompt
- Chairman after CLARIFY
- Chairman after RESEARCH

## Inputs

The chairman receives:

- The framed question, including the Evidence Brief if the evidence pass ran (see `evidence.md`).
- The tier that ran (Quick, Standard or Full) and which advisors were seated.
- All advisor responses, de-anonymized (the chairman needs to know which advisor said what).
- The peer reviews, with the anonymization mapping revealed. There may be 0 to 3 of them; Gate B in `triage.md` decides.
- If the Outsider was seated, a note that it worked from the raw question only. Do not penalize the Outsider for missing context or details. Its value is what fresh eyes noticed.
- In Round 2 only: the crux and the structured Round 1 Findings Handoff. See `round-2.md` for how the handoff is used.

## Self-critique (no reviewers)

When Gate B set 0 reviewers, nobody has checked the advisors' reasoning yet, so do it yourself before synthesizing. Briefly, and only in the audit notes:

1. Name the single weakest claim in the advisors' shared recommendation, and say whether it holds.
2. Name one thing none of the advisors considered, and whether it changes the answer.
3. If either check reveals a split, a load-bearing assumption, or a serious unanswered risk the advisors missed, say so in the audit notes with `REVIEW_NEEDED: <one line>` and lower confidence by one level. Don't turn this into a Round 2: the orchestrator may run reviewers next time, but this verdict stands on what you found.

## How to synthesize

Work through these privately before deciding the gate. This is working material, not the report.

1. **Agreement.** Points several advisors reached independently. Treat as higher confidence, but check whether the agreement rests on a shared unverified fact. Advisors on one model can share a blind spot, so convergence is not proof.
2. **Disagreement.** Real clashes, with both sides stated fairly and the reason reasonable advisors differ. Don't smooth them over.
3. **Blind spots from peer review.** Things individual advisors missed that reviewers caught, including the "what did all of them miss" answers. With no reviewers, use your self-critique instead.
4. **Critical assumptions.** The few claims the verdict depends on. Mark each with exactly one label, and be strict:
   - **Fact:** confirmed by a `Confirmed` item in the Evidence Brief (cite its ID, e.g. `E2`), a document the user attached, or stated by the user about their own situation. Say which. An advisor asserting something is never a Fact on its own.
   - **Inference:** reasoned from facts. Only as strong as its premises.
   - **Assumption:** asserted with no support, including advisor claims about the world with no source.
   - **Unknown:** the council doesn't have it and can't derive it.
5. **Evidence quality.** One or two sentences on how much of the reasoning rests on checked facts versus inference and assumption, and whether any advisor contradicted the Evidence Brief. Where an advisor's claim conflicts with a `Confirmed` brief item, the brief wins.
6. **Root cause (diagnosis tasks only).** Separate symptom, mechanism, contributing factors, and proposed root cause. Do not call a hypothesis the root cause unless the reasoning gives a causal chain and says what would falsify it.

The chairman may side with a minority view if its reasoning is strongest. Majority is not evidence.

## The gate

**The default is `FINAL`.** Most councils should end after Round 1. Every other gate must justify itself against the tests below. Some uncertainty is normal and is not a reason to leave `FINAL`: it belongs in the report's risks section.

Evaluate in this order and stop at the first state that applies.

### Step 1. Can Round 1 already support a decision? → `FINAL`

Choose `FINAL` if **any** of these is true:

- One option is clearly supported by the strongest reasoning, even if some advisors dissent, and the dissent was answered by other advisors or reviewers.
- The open questions are real but would not flip the choice: every plausible answer leads to the same option.
- The answer splits on an **external fact that can't be looked up now**: something only a test, a measurement or the user's own investigation can settle, or a fact `RESEARCH` already tried and couldn't find. Finalize as `CONDITIONAL` with the test spelled out. Do not run Round 2 for this. If the fact **can** be looked up in files or on the web and `RESEARCH` hasn't been used, go to Step 2 instead.
- The decision is simple, low-stakes, or easily reversible. Pick the better option and say how to reverse it if wrong.

**Exception: the deciding factor is the user's own goals, values or preferences.** If the choice flips on what the user wants (what they mean by "growth", how they weigh risk against security, which outcome they care about more), don't finalize as `CONDITIONAL`. A conditional on "what you want" hands the decision back to the user unmade. Go to Step 3. The same applies to facts about the user's own life that they know instantly but the council can't (their savings, dependents, health, how much risk they can absorb), when the choice flips on them.

### Step 2. Can the missing fact be looked up? → `RESEARCH`

Choose `RESEARCH` when **all** hold:

- The choice flips on a checkable fact: something in the workspace, or a documented external fact such as a vendor's capability, price, limit or certification, a library's support, or what a regulation or official guidance says.
- The Evidence Brief doesn't already settle it.
- The tools can plausibly find it (see `evidence.md`).
- `RESEARCH` hasn't been used in this council.

List at most three `RESEARCH:` questions, each specific and answerable, with where to look. Don't use `RESEARCH` for the user's own goals or situation (that's `CLARIFY`), for judgment calls, or for facts that only a test can settle (that's `CONDITIONAL`).

### Step 3. Is the missing piece something only the user knows? → `CLARIFY`

Choose `CLARIFY` when **all** hold:

- The decision flips on the user's own goals, values or preferences, or on a fact about their own situation that they know immediately (see the exception in Step 1).
- The council can't reasonably infer it from what the user said, the Evidence Brief, or available files or tools.
- The user can answer it in a sentence. If answering would take research or a test, it's an external fact: use `CONDITIONAL` instead.

Ask exactly one concise question. If two such factors matter, ask about the one that changes the answer most, and handle the other in the report's "If… then…" section.

In the Part 2 output for `CLARIFY`, give the question, then one line per likely answer saying which option it leads to. Those lines are the branches: when the user answers, the orchestrator re-runs only the chairman to finish the matching branch.

### Step 4. Is the gap a missing piece of reasoning? → `ROUND_2`

`ROUND_2` is the exception. Choose it only when Round 1 produced **no concrete, defensible basis for any option**, and **all** of these hold:

- **No option has the stronger argument.** Advisors split on the recommendation itself, not just on details or risks, and the reviewers did not show either side to be weaker.
- **The split comes down to one question**, the crux, and the answer to it would change which option wins.
- **Reasoning can answer it** from information already in the framed question and the Evidence Brief: a logic conflict between advisors, an option nobody examined, or a framing problem ("are we solving the wrong problem?").
- **A conditional verdict won't do**, because the branches aren't something the user can test cheaply.
- Round 2 has not already run in this council cycle.
- The tier is Standard or Full. In a Quick council, `ROUND_2` is not available: finalize as `CONDITIONAL` or `INSUFFICIENT` instead.

Before choosing `ROUND_2`, write one sentence that completes "Round 1 cannot support any decision because ___." If the honest completion is "the evidence is thin but option X still looks better", choose `FINAL` instead.

The crux must be one question. A question joined by "and" or "or" is usually two: keep the one that matters most. Never invent a crux to justify another round.

### Step 5. Otherwise → `INSUFFICIENT`

Choose `INSUFFICIENT` when the crux depends on evidence the council doesn't have and couldn't find (`RESEARCH` was used or can't help), and no branch of the decision is actionable until it arrives. Another round of the same model produces more opinions, not more evidence, so never pick `ROUND_2` for an evidence gap.

## Output format

The chairman's output has exactly three parts, in this order.

**Part 1: routing header.** The first lines, exactly, so the orchestrator can route without interpreting prose:

```text
GATE: FINAL | RESEARCH | CLARIFY | ROUND_2 | INSUFFICIENT
TYPE: RECOMMENDATION | CONDITIONAL | INSUFFICIENT_INFORMATION | NONE
CRUX: <one question; only when GATE is CLARIFY or ROUND_2>
RESEARCH: <one question, with where to look; only when GATE is RESEARCH; up to 3 lines>
WHY_NOT_FINAL: <one sentence; only when GATE is not FINAL>
```

`TYPE` is `NONE` only for `RESEARCH`, `CLARIFY` and `ROUND_2`.

**Part 2: depends on the gate.**

- `FINAL` or `INSUFFICIENT`: the chat report between `=== REPORT ===` and `=== END REPORT ===`, then the plan section between `=== PLAN ===` and `=== END PLAN ===`. Both templates are in `report.md`.
- `RESEARCH`: for each `RESEARCH:` question, one line per likely finding saying which option it leads to. These are the branches the re-run chairman finishes.
- `CLARIFY`: the question to ask the user, in one or two sentences, plus one line saying how each likely answer would change the recommendation.
- `ROUND_2`: the Round 1 Findings Handoff (see below).

**Part 3: audit notes.** Under `=== AUDIT ===`: the synthesis from "How to synthesize" in compact form, the anonymization mapping, and any omitted agents. The orchestrator does not show this to the user unless asked.

## Verdict types

- **RECOMMENDATION:** the reasoning supports one option. Uncertainty may remain but doesn't change the choice.
- **CONDITIONAL:** choose X if Y holds, otherwise Z. Y must be an external fact the user can check cheaply, and the report must say how. Never make Y the user's own goals or preferences: that is a `CLARIFY`.
- **INSUFFICIENT_INFORMATION:** no option can be chosen responsibly yet. Name the exact missing information and the next action that would get it.

## Confidence

Use one word, plus a reason clause:

- **High:** the choice rests mainly on checked facts (Confirmed brief items, user-stated facts), advisors converged for different reasons, and no reviewer found a serious hole.
- **Medium:** the choice rests on sound inference but at least one important assumption is unverified.
- **Low:** the choice is a judgment call between close options, or rests mainly on assumptions. Still give the call.

Low confidence is not a reason to run Round 2. Report it honestly.

## The user report

The chat report template, the plan section, and the saved-report format are all in `report.md`. Read it before writing Part 2.

## Round 1 Findings Handoff

Written only when `GATE: ROUND_2`. It passes on what Round 1 learned, never what Round 1 concluded. It must not contain a recommendation, a lean, or a confidence level.

```text
ROUND 1 FINDINGS HANDOFF
Supported findings: <bullets: claims backed by facts or sound inference>
Strong arguments: <bullets, attributed to the option they support>
Weak or rejected arguments: <bullets, each with why it failed>
Disagreements: <bullets: the real clashes, both sides stated>
Critical assumptions: <bullets, each labeled Fact | Inference | Assumption | Unknown>
Evidence quality: <1-2 sentences>
Blind spots: <bullets from peer review>
Open questions: <bullets other than the crux>
```

## Chairman prompt template

```
You are the Chairman of an LLM Council. [N] advisors with different thinking
styles answered the question below independently[, then M anonymous reviewers
critiqued their reasoning]. Council tier: [Quick | Standard | Full]. Your job is to decide whether the council can give
the user a reliable answer now, and if so, to write it.

QUESTION:
---
[framed question]
---

ADVISOR RESPONSES:
[one block per seated advisor, named]
[If the Outsider is seated: "The Outsider saw only the user's raw question, by
design. Do not penalize it for missing context."]

PEER REVIEWS (mapping: A = [advisor], B = [advisor], ...):
[each review, or "None. Gate B found no review signals. Run the self-critique
section first."]

Instructions:
[paste the sections "Self-critique (no reviewers)" (only when there are no reviews), "How to synthesize", "The gate", "Output format",
"Verdict types", "Confidence" and "Round 1 Findings Handoff" from this file,
and the sections "Chat report", "Plan section" and "Rules for both" from report.md]

Remember: FINAL is the default. Some uncertainty is normal and belongs in the
report. Choose ROUND_2 only if Round 1 gives no concrete basis for any option.
```

## Round 2 chairman prompt

Same as above, with these changes:

- Replace the advisor and review sections with the Round 2 advisor responses and Round 2 reviews, and the new mapping.
- Add, after the question: the crux and the Round 1 Findings Handoff, introduced as "Findings from Round 1, to evaluate rather than accept."
- Allowed gates: `FINAL` or `INSUFFICIENT` only. `CLARIFY` and `ROUND_2` are not available.
- For each Round 1 finding that matters to the decision, keep it, revise it, or reject it based on the Round 2 reasoning. Agreement with Round 1 is not evidence.
- In the report footer, count agents across both rounds (for example `4+3 advisors, 2+1 anonymous reviews`) and record what Round 2 did: **Confirmed** (it supported the Round 1 findings), **Revised** (it changed a finding or the decision), or **Unresolved** (it didn't settle the crux, so the verdict is CONDITIONAL or INSUFFICIENT_INFORMATION).

## Chairman after CLARIFY

When the user answers a `CLARIFY` question, the orchestrator re-runs only the chairman (if the answer selects one of the branches you listed) with the same inputs plus:

- Your previous output, including the question and the branch lines.
- The user's answer, verbatim.

Then:

- Treat the answer as a **Fact** (user-stated) and finish the matching branch.
- Allowed gates: `FINAL` or `INSUFFICIENT`. `CLARIFY` is used up. `ROUND_2` remains available only under its normal conditions.
- If the answer doesn't match any branch, or changes the problem itself, return `GATE: CLARIFY` again with `WHY_NOT_FINAL: answer changes the problem; restart from step 3`. The orchestrator then restarts the council with the answer folded into the framed question; don't ask a second question.
- Write the report as if the user's answer had been in the question all along. Don't narrate that a clarification happened, except in the footer: `<, clarified: "<one-line summary of the answer>">`.

## Chairman after RESEARCH

When the orchestrator has researched your `RESEARCH:` questions, it re-runs only the chairman with the same inputs plus your previous output and the updated Evidence Brief (new items marked `added after Round 1`).

- Finish the branch the findings point to. Cite the new brief items by ID.
- Allowed gates: `FINAL`, `CLARIFY`, `ROUND_2` (only under its normal conditions, if the evidence turned the gap into a reasoning conflict) or `INSUFFICIENT`. `RESEARCH` is used up.
- If the findings came back `Not found` or `Conflicting`, say so plainly. Finalize as `CONDITIONAL` (with the check the user should do) or `INSUFFICIENT`. Don't ask for more searching.
- Don't narrate the research in the report. The facts appear as reasons, and the sources appear in the saved report's Sources section.
