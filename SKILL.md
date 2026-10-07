---
name: llm-council
description: "Run a question, idea, or decision through a council of 5 AI advisors who analyze it independently, review each other anonymously, and pass through a chairman who either delivers a verdict or says what is still missing. Based on Karpathy's LLM Council. MANDATORY TRIGGERS: 'council this', 'run the council', 'war room this', 'pressure-test this', 'stress-test this', 'debate this'. STRONG TRIGGERS (when paired with a real decision or tradeoff): 'should I X or Y', 'which option', 'what would you do', 'is this the right move', 'validate this', 'get multiple perspectives', 'I can't decide', 'I'm torn between'. Do NOT trigger on factual lookups, simple yes/no questions, creation tasks, or casual 'should I' questions with no real tradeoff. DO trigger when the user presents a genuine decision with stakes, several options, and context suggesting they want it pressure-tested from multiple angles."
---

# LLM Council

One model gives one answer, and you can't tell whether it is strong or mid. The council runs a question through five advisors with deliberately different thinking styles, has them attack each other's reasoning anonymously, and has a chairman decide whether the result is reliable enough to act on.

**Principle:** optimize for the most reliable decision the available information allows, not for producing an answer. If a single unresolved crux can be resolved through another round of reasoning, run it once. If the crux requires external evidence or more reasoning won't help, stop. If real-world evidence is needed, say exactly what, and don't manufacture certainty to fill the gap. Keep this in mind for any situation the reference files don't cover.

## When to use it

Use it when being wrong is expensive and there is real uncertainty: a choice between options, a pivot, a pricing or positioning call, a plan or draft the user wants stress-tested. Skip it for questions with one right answer, writing tasks, and summaries. Just answer those.

## Workflow

Read each reference file when you reach its stage, not before.

1. **Scan context.** Read only workspace files that bear on this question (CLAUDE.md, memory, files the user attached or named, data relevant to the decision). Skip unrelated files. Do not read past council transcripts unless the user asks. If they do, treat old conclusions as hypotheses to challenge, not facts, because early conclusions otherwise anchor every later run.
2. **Frame the question.** Write one neutral prompt: the decision, the user's context, relevant file context, and what's at stake. Don't steer. If the question is too vague to frame, ask one clarifying question, then proceed.
3. **Advisors (parallel).** Spawn all five at once. Four get the framed question. **The Outsider gets only the user's raw question** with the trigger phrase stripped (e.g. remove "council this:"), because an advisor given the full context isn't an outsider. See `references/advisors.md`.
4. **Anonymous peer review (parallel).** Randomize advisor-to-letter mapping, then run five reviewers. See `references/peer-review.md`.
5. **Chairman.** Synthesize everything and return a gate decision. Tell the chairman the Outsider had less context by design, so it doesn't penalize that response for missing detail. See `references/chairman.md`.
6. **Route on the chairman's first line**, which is always `GATE: FINAL | ROUND_2 | INSUFFICIENT`:
   - `FINAL`: the verdict is solid. Go to step 7.
   - `ROUND_2`: the chairman named one crux that reasoning can resolve. Run `references/round-2.md`, then go to step 7.
   - `INSUFFICIENT`: the crux needs external evidence. Skip Round 2 and go to step 7.
7. **Report.** Present the final verdict to the user. Include the reasoning needed to understand the decision, the confidence, main uncertainty, and what would change the verdict.

The gate's definitions and criteria live only in `references/chairman.md`. Don't re-derive them here or elsewhere.

## Round 2 cap

Round 2 is the final reasoning pass, and there is at most one per council run. Afterward the only valid gates are `FINAL` or `INSUFFICIENT`, and any remaining uncertainty goes into the verdict. If the Round 2 chairman returns `ROUND_2` anyway, re-prompt it once to finalize. If it still does, report `INSUFFICIENT` with the crux as the missing information. If the user wants to go further, they start a new council with new evidence, not a third round.

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
- **User supplies new information mid-run:** fold it into the framed question and restart from step 3. Don't patch a finished run.