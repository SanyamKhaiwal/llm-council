# Report

The council produces two outputs from one chairman run:

1. **The chat report:** a short verdict shown to the user in the conversation.
2. **The saved report:** a Markdown file holding the question, the decision, the reasoning behind it, and the plan to carry it out. This is the lasting record of the council's work.

Neither output contains the deliberation itself (advisor responses, peer reviews, anonymization mappings). The saved report keeps conclusions and actions, not conversations.

## Contents

- Chat report
- Plan section (chairman writes)
- Saved report
- Rules for both

## Chat report

Readable in under two minutes by someone who didn't see the deliberation. 250 to 450 words. Use this template exactly, and omit any section that has no real content:

```markdown
## Verdict: <the decision in one plain sentence, 25 words at most>

**Confidence:** <High | Medium | Low>, <one short reason>

<2-3 sentences: the core reasoning. Why this option beats the alternatives for this user specifically.>

### Why
- <reason 1, concrete>
- <reason 2>
- <reason 3, optional>

### If… then… (CONDITIONAL only)
- **If <condition>:** <do X>
- **If not:** <do Y>
- **How to check:** <cheap test, ideally under a day>

### Watch out for
- <the biggest risk and how to reduce it>
- <a second risk, only if it matters>

### What would change this
<One or two sentences: the specific new fact that would flip the verdict.>

### Next step
<One concrete action the user can take this week.>

---
<sub>Council: <Quick | Standard | Full>, <N> advisors, <M> anonymous reviews<, Round 2 on: "<crux>" → Confirmed | Revised | Unresolved><, clarified: "<one-line summary of the user's answer>"><. Notes: reduced independence / omitted agents, if any></sub>
```

Footer counts: after Round 2, show both rounds as "Round 1 + Round 2", for example `Council: Standard, 4+3 advisors, 2+1 anonymous reviews, Round 2 on: "..." → Revised`. Without Round 2, show single numbers.

For `INSUFFICIENT_INFORMATION`, replace "Why" with "### What's missing" (the exact information and why it decides the question) and make "Next step" the action that gets that information. Still say which way the council leans, if it does, and how strongly.

## Plan section (chairman writes)

Between `=== PLAN ===` and `=== END PLAN ===`, the chairman writes the material that goes only into the saved report. Fit it to what the question asked for: a choice between options needs the alternatives and an implementation plan, a diagnosis needs the root cause and the fix, and a stress-tested draft needs the specific changes.

```markdown
### Options considered
- **<Chosen option>** (chosen): <one line on why>
- **<Alternative>**: <one line on why it lost, and when it would be the better choice>

### Key assumptions
- <assumption the decision depends on> (<Fact | Inference | Assumption | Unknown>)

### Implementation plan
1. <step: concrete action, owner if known, rough timing>
2. <step>
3. <step>

### Checkpoints
- <signal to watch, and when to look at it> → <what to do if it goes wrong>

### Open questions
- <anything still unresolved that the user should keep in mind>

### Sources
- E1: <title or file path>, <publisher>, <date> — <URL or path>
- <one line per Evidence Brief item the verdict relies on; write "No external sources checked" and why, if none>
```

The implementation plan has 3 to 8 steps, in order, specific to the user's situation. Each step must be something the user can actually do. For `CONDITIONAL`, give a short plan for each branch, starting with the check. For `INSUFFICIENT_INFORMATION`, the plan is the steps to get the missing information and the decision to make once it arrives.

## Saved report

After showing the chat report, the orchestrator writes the saved report:

- **Location:** `council-reports/` in the current working directory. Create the folder if it doesn't exist.
- **Filename:** `YYYY-MM-DD-<short-kebab-slug>.md`, for example `2026-10-10-pricing-change.md`. If the name exists, add `-2`.
- **Content:** assemble from the chairman's output. Don't rewrite or summarize it:

```markdown
# <Short title of the decision>

**Date:** <YYYY-MM-DD> · **Verdict type:** <Recommendation | Conditional | Insufficient information> · **Confidence:** <High | Medium | Low>

## Question
<The user's question as framed for the council, with the context that matters. Leave out private workspace details that aren't needed to understand the decision.>

<The chat report body, from "## Verdict" through "Next step", with the footer line removed>

<The plan section from the chairman>

---
<The chat report's footer line>
```

After writing it, tell the user in one line where it was saved.

If the user has said not to save files, or the workspace is read-only, skip the file and say so in one line.

## Rules for both

- Lead with the answer. Plain language. The verdict line is one sentence of at most 25 words. Detail goes in the sections below it.
- No mention of the council's machinery in the chat report: no advisor names, the words "advisor" or "reviewer", response letters, gate names, or internal labels. Give reasons in terms of the user's situation ("a file outside the repo can't be committed"), not in terms of who agreed. The footer line is the only exception. The only labels allowed are the Fact/Inference/Assumption/Unknown tags in the saved report's "Key assumptions".
- Specific to the user's situation. No generic advice.
- Back factual reasons with evidence. In the chat report, when a reason rests on a checked fact, say so briefly in plain words ("Supabase's paid plans include a SOC 2 report"), not with brief IDs. The saved report lists the sources. If a key fact couldn't be checked, the report says so.
- The saved report must make sense to someone reading it months later with no other context.
- Saved reports are a record for the user. Don't read them into future councils unless the user asks, and then treat them as hypotheses to challenge, not facts.
