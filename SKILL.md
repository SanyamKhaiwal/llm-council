---
name: llm-council
description: "Run a question, idea, or decision through a council of AI advisors with deliberately different thinking styles, who analyze it independently, are reviewed anonymously when the disagreement or stakes justify it, and pass through a chairman who either delivers a verdict or says what is still missing. The council sizes itself to the question, so simple decisions stay cheap. MANDATORY TRIGGERS: 'council this', 'run the council', 'war room this', 'pressure-test this', 'stress-test this', 'debate this'. STRONG TRIGGERS (when paired with a real decision or tradeoff): 'should I X or Y', 'which option', 'is this the right move', 'validate this', 'get multiple perspectives', 'I can't decide', 'I'm torn between'. Do NOT trigger on factual lookups, simple yes/no questions, creation tasks, or casual 'should I' questions with no real tradeoff. DO trigger when the user presents a genuine decision with stakes, several options, and context suggesting they want it pressure-tested from multiple angles."
---

# LLM Council

One model gives one answer, and you can't tell whether it is strong or mid. The council runs a question through advisors with deliberately different thinking styles, has their reasoning attacked anonymously when that is worth the cost, and has a chairman decide whether the result is reliable enough to act on.

**Principle:** optimize for the most reliable decision the available information allows, at the lowest cost that achieves it. Check facts instead of assuming them: when a claim the decision depends on can be looked up in the workspace or on the web, look it up and cite it. Spend more agents only where they can change the answer. If a single unresolved crux can be resolved through another round of reasoning, run it once. Round 2 builds on useful findings from Round 1 without inheriting its conclusion or confidence. If the crux is a user-specific fact that could materially change the decision, ask the user one clarification question. If the crux requires external evidence or more reasoning won't help, stop, say exactly what is missing, and don't manufacture certainty to fill the gap. Keep this in mind for any situation the reference files don't cover.

## When to use it

Use it when being wrong is expensive and there is real uncertainty: a choice between options, a pivot, a pricing or positioning call, an architecture decision, a plan or draft the user wants stress-tested. Skip it for questions with one right answer, writing tasks, and summaries. Just answer those.

## Workflow

Read each reference file when you reach its stage, not before. "Sub-agent" means any independent model call your environment provides. If there are none, see Failure modes.

1. **Scan context.** Read only files that bear on this question (project instructions, memory, files the user attached or named, data relevant to the decision). Skip unrelated files. Do not read past council transcripts or files in `council-reports/` unless the user asks. If they do, treat old conclusions as hypotheses to challenge, not facts.
2. **Frame the question.** Write one neutral prompt: the decision, the user's context, relevant file context, and what's at stake. Don't steer. If the question is too vague to frame, ask one clarifying question, then proceed. This is separate from the chairman's `CLARIFY` gate.
3. **Gate A: size the council.** Score the question and pick a tier (Direct, Quick, Standard or Full), which sets how many advisors run and which ones. Tell the user the tier in one line. If the tier is Direct, answer the question yourself and offer to run the council; stop here. See `references/triage.md`.
3b. **Evidence pass.** List the checkable facts the decision depends on, look them up (workspace first, then the web) within the tier's budget, and add an Evidence Brief with sources to the framed question. The Outsider never sees it. Skip this if nothing is checkable. See `references/evidence.md`.
4. **Advisors (parallel).** Run the advisors the tier selected, all at once. If the Outsider is seated, it gets **only the user's raw question** with the trigger phrase stripped, because an advisor given the full context isn't an outsider. The others get the framed question. See `references/advisors.md`.
5. **Gate B: decide on review.** Check the advisor responses for the review signals (split, load-bearing assumption, unanswered serious risk, high stakes) and set the reviewer count, from 0 to 3. See `references/triage.md`.
6. **Anonymous peer review (parallel), if Gate B set reviewers.** Shuffle the advisor responses, relabel them, and run the reviewers. See `references/peer-review.md`. With 0 reviewers, skip this step.
7. **Chairman.** Synthesize and return a gate decision. Tell the chairman which tier ran, which advisors were seated, how many reviews there are, and that the Outsider (if seated) had less context by design. With 0 reviewers, the chairman runs its self-critique first. See `references/chairman.md`.
8. **Route on the chairman's first line**, which is always `GATE: FINAL | RESEARCH | CLARIFY | ROUND_2 | INSUFFICIENT`:
   - `FINAL`: go to step 9.
   - `RESEARCH`: look up the chairman's `RESEARCH:` questions (at most 3), add the findings to the Evidence Brief, and re-run **only the chairman**. Allowed once per council. See `references/evidence.md`.
   - `CLARIFY`: ask the user exactly one concise question. When they answer:
     - If the answer selects one of the branches the chairman already described, re-run **only the chairman** with the answer added. Don't re-run advisors.
     - If it changes the problem itself, fold it into the framed question and restart from step 3.
     - `CLARIFY` is allowed at most once per council.
   - `ROUND_2`: rare, and only in Standard or Full tiers. Run the Round 2 check below. If it passes, follow `references/round-2.md`, then go to step 9. In a Quick tier, the chairman must finalize instead.
   - `INSUFFICIENT`: go to step 9.

   **Round 2 check.** `FINAL` is the expected outcome for most councils. Before starting Round 2, confirm the chairman's `WHY_NOT_FINAL:` line says Round 1 gives no concrete basis for any option. If instead it describes ordinary uncertainty, a checkable assumption, or a risk, send it back to the chairman once with: "This does not meet the Round 2 bar. Finalize as RECOMMENDATION or CONDITIONAL." Don't run Round 2 because the question feels important.
9. **Report and save.** Read `references/report.md`. Show the user only the text between `=== REPORT ===` and `=== END REPORT ===`, exactly as written. Don't add advisor responses, reviews, gate names, or audit notes, and don't write your own summary around it. Then save the decision record to `council-reports/` as `report.md` describes, and tell the user the path in one line. If the user asks how the council got there, show the audit notes then.

The gate's definitions and criteria live only in `references/chairman.md`, the sizing and review rules only in `references/triage.md`, and the research rules only in `references/evidence.md`. Don't re-derive them elsewhere.

## Cost discipline

- Every sub-agent has a fixed setup cost, so the number of agents matters more than the length of their answers. Gates A and B exist to keep that number down.
- Pass long material (advisor responses for reviewers and the chairman) as files where your environment allows it, rather than pasting it into every prompt.
- Reuse work. When the review count goes up, never re-run advisors who already answered.
- Evidence costs tool calls (searches, file reads), not advisor calls. It's the cheapest way to raise confidence, so prefer checking a fact over adding a reviewer to argue about it.
- A typical Quick council is 4 calls. Full with Round 2 tops out at 14. See the call budget in `references/triage.md`.

## Round 2 cap

Round 2 is the exception, not a normal step. It runs only in Standard or Full tiers, only when Round 1 produced no concrete basis for choosing any option, and at most once per council, including after a `CLARIFY` restart.

Round 2 is **not a reset**. It receives a structured handoff of Round 1's findings: supported findings, strong and weak arguments, disagreements, critical assumptions, evidence quality, and blind spots. It must **not** inherit the Round 1 verdict, confidence, or lean. The findings are evidence to examine, not conclusions to defend.

After Round 2, the chairman may return `FINAL` or `INSUFFICIENT`. Do not run a third round.

## Verdict types

Every final verdict is exactly one of these (definitions in `references/chairman.md`):

- **RECOMMENDATION**: enough evidence to choose.
- **CONDITIONAL RECOMMENDATION**: choose X if assumption Y holds, with a cheap way to test Y.
- **INSUFFICIENT INFORMATION**: can't reliably choose yet. Names the missing information and the next action.

Each verdict states confidence, the main uncertainty, and what would change it.

## Failure modes

- **No sub-agents available:** run each advisor as a separate, clearly delimited pass, each written without reference to earlier ones. Reviewers and the chairman follow the same way. Gates A and B apply unchanged and matter even more here, since every pass adds to one context. Note in the report that independence is reduced.
- **A sub-agent returns something malformed or off-task:** re-run that one agent once. If it fails again, proceed without it and disclose the omission in the report.
- **No search or file tools:** skip what can't be done, list those facts as unchecked in the Evidence Brief, and say in the report that the verdict wasn't checked against external sources. Never present an unchecked claim as checked.
- **Advisors converge suspiciously fast:** advisors sharing one model can share a bias. Agreement is not confirmation. On high-stakes questions Gate B still runs reviewers for this reason, and the chairman should say so when agreement rests on unverified facts.
- **User supplies new information mid-run:** if it answers the chairman's `CLARIFY` question, handle it as in step 8. Otherwise finish the current run and offer a new council. Do not patch a finished council.
