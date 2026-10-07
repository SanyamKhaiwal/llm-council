# Chairman

The chairman turns five advisor responses and five peer reviews into a verdict, a request for one user-specific clarification, or a precise statement of what is still missing. This file is the single source of truth for the gate and the verdict types. `SKILL.md` and `round-2.md` refer here and don't restate the criteria.

## Contents

- Inputs
- How to synthesize
- The gate
- Output format
- Verdict types
- Confidence
- Chairman prompt template
- Round 2 chairman prompt

## Inputs

The chairman receives:

- The framed question.
- All five advisor responses, de-anonymized (the chairman needs to know which advisor said what).
- All five peer reviews, with the anonymization mapping revealed.
- A note that the Outsider worked from the raw question only. Do not penalize the Outsider for missing context or details. Its value is what fresh eyes noticed.
- In Round 2 only: the crux and the structured Round 1 Findings Handoff. See `round-2.md` for how the handoff is used.

## How to synthesize

Work through these before deciding the gate:

1. **Agreement.** Points several advisors reached independently. Treat as higher confidence, but check whether the agreement rests on a shared unverified fact. Five advisors on one model can share a blind spot, so convergence is not proof.

2. **Disagreement.** Real clashes, with both sides stated fairly and the reason reasonable advisors differ. Don't smooth them over.

3. **Blind spots from peer review.** Things individual advisors missed that reviewers caught, including the "what did all five miss" answers.

4. **Critical assumptions.** The few claims the verdict depends on. Mark each with exactly one label, and be strict:
   - **Verified Fact:** confirmed by a source the council can point to (a file in the workspace, a document the user attached, a figure with a citation).
   - **User-Stated Fact:** the user said it about their own situation. Reliable enough to build on, but not independently checked.
   - **Evidence:** data or observations that support a claim but do not by themselves settle it. Evidence may come from workspace files, user-provided material, or the council's reasoning; do not treat an advisor's unsupported assertion as evidence.
   - **Inference:** reasoned from other claims. Only as strong as its premises.
   - **Assumption:** asserted with no support, including advisor claims about the world with no source.
   - **Unknown:** the council doesn't have it and can't derive it.

5. **Evidence quality.** One or two sentences on how much of the reasoning rests on verified facts versus inference.

6. **Root-cause analysis.** For diagnosis or debugging tasks, distinguish the symptom, mechanism, contributing factors, and proposed root cause. Do not call a hypothesis the root cause unless the reasoning identifies a causal chain and states what evidence would falsify it.

The chairman may side with a minority view if its reasoning is strongest. Majority is not evidence.

## The gate

After synthesizing, the chairman picks exactly one state. Evaluate in this order:

### 1. Is there a crux that would change the answer?

The crux is exactly one decision-relevant question: the unresolved question that, if answered differently, would flip or materially weaken the recommendation.

No compound cruxes. A question joined by "and" or "or" is usually two questions. Split it and identify the single question whose answer could most materially change the recommendation; move the remaining issues to Critical Assumptions or What Could Change the Verdict.

Do not manufacture a crux merely to justify another round. If no such question exists, the answer is `FINAL`. Don't invent a crux to justify another round.

### 2. Can the crux be resolved by user clarification?

Choose `CLARIFY` when the crux depends on a user-specific fact, preference, constraint, goal, or other information that only the user can reliably provide, and that information could materially change the decision.

The clarification must:

- Ask exactly one concise question.
- Request information the council cannot reliably infer.
- Be directly relevant to the decision.
- Be capable of materially changing the recommendation.
- Not ask for information that could reasonably be obtained through available evidence or tools.

If clarification would not materially affect the decision, do not ask it.

### 3. If not, can reasoning resolve the crux?

A crux is reasoning-resolvable when it is a conflict in logic between advisors, an option nobody examined properly, a framing question ("are we solving the right problem?"), or an assumption that advisors could test against the information already in the framed question.

### 4. Pick the state

- `GATE: FINAL` when no unresolved crux could materially change the recommendation, or when the remaining uncertainty does not prevent a useful recommendation and is explicitly disclosed. Uncertainty is acceptable only when every plausible value of the unknown leads to the same choice, or when the decision can be made conditionally with actionable branches. If the uncertainty affects the decision and the user can check it cheaply, finalize as a Conditional Recommendation instead.

- `GATE: CLARIFY` when the crux depends on one user-specific piece of information that could materially change the decision and the user can provide it directly. Ask exactly one concise question.

- `GATE: ROUND_2` only when **all** of these hold:
  - You can name exactly one decision-relevant question as the crux.
  - It is reasoning-resolvable.
  - The verdict can't be firm without it.
  - Round 2 has not already run in this council cycle.

- `GATE: INSUFFICIENT` when the crux is evidence-dependent and no branch of the decision is actionable until the information arrives, or when more reasoning is unlikely to improve the answer.

If the crux is evidence-dependent, do not choose `ROUND_2` even when it is tempting. Another round of the same model produces more opinions, not more evidence.

## Output format

The first lines of the chairman's output are always these fields, exactly, so the orchestrator can route without interpreting prose:

```text
GATE: FINAL | CLARIFY | ROUND_2 | INSUFFICIENT
TYPE: RECOMMENDATION | CONDITIONAL | INSUFFICIENT_INFORMATION | NONE
CRUX: <exactly one decision-relevant question when GATE is CLARIFY or ROUND_2; otherwise omit this line>