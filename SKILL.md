---
name: llm-council
description: "Run a question, idea, or decision through a council of 5 AI advisors who analyze it independently, review each other anonymously, and pass through a chairman who either delivers a verdict or says what is still missing. Based on Karpathy's LLM Council. MANDATORY TRIGGERS: 'council this', 'run the council', 'war room this', 'pressure-test this', 'stress-test this', 'debate this'. STRONG TRIGGERS (when paired with a real decision or tradeoff): 'should I X or Y', 'which option', 'what would you do', 'is this the right move', 'validate this', 'get multiple perspectives', 'I can't decide', 'I'm torn between'. Do NOT trigger on factual lookups, simple yes/no questions, creation tasks, or casual 'should I' questions with no real tradeoff. DO trigger when the user presents a genuine decision with stakes, several options, and context suggesting they want it pressure-tested from multiple angles."
---

# LLM Council

One model gives one answer, and you can't tell whether it is strong or mid. The council runs a question through five advisors with deliberately different thinking styles, has them attack each other's reasoning anonymously, and has a chairman decide whether the result is reliable enough to act on.

**Principle:** optimize for the most reliable decision the available information allows, not for producing an answer. If a single unresolved crux can be resolved through another round of reasoning, run it once. Round 2 should build on useful findings from Round 1 without inheriting its conclusion or confidence: carry forward established findings, strong and weak arguments, disagreements, assumptions, evidence quality, and blind spots, while treating the unresolved crux as the central question to resolve. If the crux is a user-specific fact that could materially change the decision, ask the user one clarification question and restart the council with the answer. If the crux requires external evidence, cannot be resolved through user clarification, or more reasoning won't help, stop. If real-world evidence is needed, say exactly what, and don't manufacture certainty to fill the gap. Keep this in mind for any situation the reference files don't cover.

## When to use it

Use it when being wrong is expensive and there is real uncertainty: a choice between options, a pivot, a pricing or positioning call, a plan or draft the user wants stress-tested. Skip it for questions with one right answer, writing tasks, and summaries. Just answer those.

## Workflow

Read each reference file when you reach its stage, not before.

1. **Scan context.** Read only workspace files that bear on this question (CLAUDE.md, memory, files the user attached or named, data relevant to the decision). Skip unrelated files. Do not read past council transcripts unless the user asks. If they do, treat old conclusions as hypotheses to challenge, not facts, because early conclusions otherwise anchor every later run.
2. **Frame the question.** Write one neutral prompt: the decision, the user's context, relevant file context, and what's at stake. Don't steer. If the question is too vague to frame, ask one clarifying question, then proceed. This initial clarification is separate from the chairman's `CLARIFY` gate.
3. **Advisors (parallel).** Spawn all five at once. Four get the framed question. **The Outsider gets only the user's raw question** with the trigger phrase stripped (e.g. remove "council this:"), because an advisor given the full context isn't an outsider. See `references/advisors.md`.
4. **Anonymous peer review (parallel).** Randomize advisor-to-letter mapping, then run five reviewers. See `references/peer-review.md`.
5. **Chairman.** Synthesize everything and return a gate decision. Tell the chairman the Outsider had less context by design, so it doesn't penalize that response for missing detail. If the gate is `ROUND_2`, the chairman must also produce a structured Round 1 findings handoff containing the useful findings that Round 2 should inherit, while keeping the Round 1 conclusion and confidence out of that handoff. See `references/chairman.md`.
6. **Route on the chairman's first line**, which is always `GATE: FINAL | CLARIFY | ROUND_2 | INSUFFICIENT`:
   - `FINAL`: the verdict is solid. Go to step 7.
   - `CLARIFY`: the chairman identified one user-specific piece of information that could materially change the decision. Ask the user exactly one concise clarification question. After the user answers, fold the answer into the framed question and restart from step 3. A council cycle may use the `CLARIFY` gate at most once.
   - `ROUND_2`: the chairman named one crux that reasoning can resolve. Pass the structured Round 1 findings handoff and the crux to `references/round-2.md`. Round 2 must use those findings as context, but must independently evaluate them rather than treating the Round 1 conclusion as established. Then go to step 7.
   - `INSUFFICIENT`: the crux requires external evidence or cannot be resolved through further reasoning or user clarification. Go to step 7.
7. **Report.** Present the final verdict to the user. Include the reasoning needed to understand the decision, the confidence, main uncertainty, and what would change the verdict.

The gate's definitions and criteria live only in `references/chairman.md`. Don't re-derive them here or elsewhere.

## Round 2 cap

Round 2 is the final additional reasoning pass, and there is at most one per council run.

Round 2 is **not a reset**. It receives a structured handoff from Round 1 containing useful findings such as established findings, strong and weak arguments, disagreements, critical assumptions, evidence quality, and blind spots.

Round 2 must **not** inherit the Round 1 verdict, confidence, or current leaning as established truth. The Round 1 findings are evidence to examine, not conclusions to defend.

The Round 2 advisors should use the inherited findings to avoid repeating work that has already been done while actively challenging anything that remains uncertain or weakly supported.

After Round 2, the chairman may return `FINAL`, `CLARIFY`, or `INSUFFICIENT`. If the chairman returns `CLARIFY`, ask the user one concise question, incorporate the answer, and restart the council from step 3. If the chairman returns `INSUFFICIENT`, report what information or evidence is missing. If the Round 2 chairman returns `ROUND_2` again, re-prompt it once to finalize. If it still does, report `INSUFFICIENT` with the crux as the missing information.

## Verdict types

Every final verdict is exactly one of these (definitions in `references/chairman.md`):

- **RECOMMENDATION**: enough evidence to choose.
- **CONDITIONAL RECOMMENDATION**: choose X if assumption Y holds, with a cheap way to test Y.
- **INSUFFICIENT INFORMATION**: can't reliably choose yet. Names the missing information and the next action.

Each verdict states confidence, the main uncertainty, and what would change it.

## Failure modes

- **No sub-agents available:** run the advisors one at a time as separate, clearly delimited passes, with each written without reference to earlier ones. Reviewers and chairman follow the same way. Note in the final verdict that independence is reduced.
- **A sub-agent returns something malformed or off-task:** re-run that one agent once. If it fails again, proceed without it and disclose the omission in the final verdict.
- **Advisors converge suspiciously fast:** all five sharing one model means agreement can be shared bias, not confirmation. The chairman should say so when agreement rests on unverified facts.
- **User supplies new information mid-run:** if it answers the chairman's CLARIFY question, fold it into the framed question and restart from step 3. Do not patch a finished council run. At most one clarification question per council cycle.