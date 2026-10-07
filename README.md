# LLM Council

A multi-agent decision-making skill that pressure-tests important questions through five AI advisors, anonymous peer review, and a chairman that decides whether the available reasoning is strong enough to act on.

Inspired by [Andrej Karpathy's LLM Council](https://x.com/karpathy/status/1962263486196867115).

The goal is **not to manufacture certainty**. The council is designed to find the most reliable decision the available information allows — including recognizing when there is not enough information to make a reliable call.

---

## The Problem

A single AI response can sound convincing even when the reasoning is weak.

It can also be heavily influenced by how a question is framed. Ask whether an idea is good and an AI may naturally search for reasons it could work. Ask whether the same idea is bad and it may produce an equally convincing case against it.

LLM Council adds multiple reasoning passes and structured disagreement before producing a verdict.

---

## How It Works

When the council is triggered, the skill follows a structured pipeline:

1. **Scans the context** — reads only workspace files and user-provided material relevant to the decision.
2. **Frames the question** — turns the user's situation into a neutral decision prompt without steering the outcome.
3. **Runs five advisors in parallel** — each approaches the problem from a deliberately different reasoning perspective.
4. **Runs anonymous peer review** — the advisors critique each other's reasoning without knowing who produced which response.
5. **Chairman evaluation** — a chairman synthesizes the council, evaluates agreement, disagreement, assumptions, evidence quality, and blind spots, then chooses a gate:
   - `FINAL`
   - `ROUND_2`
   - `INSUFFICIENT`
6. **Optional Round 2** — if one unresolved crux can be settled through further reasoning alone, the council gets one additional reasoning pass.
7. **Final verdict** — the user receives a recommendation, conditional recommendation, or insufficient-information result, together with confidence, the main uncertainty, and what would change the verdict.

### The Outsider

One advisor deliberately receives only the user's raw question rather than the full framed context.

This creates a useful counterweight to the other advisors: it can expose assumptions, missing context, or obvious alternatives that become invisible once a problem has been heavily framed.

---

## The Council's Safety Valves

The council is deliberately designed to avoid turning agreement into false confidence.

### It can say "we don't know"

If the available reasoning cannot reliably resolve the decision, the final verdict can be:

**INSUFFICIENT INFORMATION**

The council identifies what information is missing and what should be done next rather than inventing certainty.

### Reasoning is not evidence

The council distinguishes between:

- **Evidence** — data, observations, workspace material, or user-provided information supporting a claim.
- **Reasoning** — conclusions and inferences produced by the advisors.

Five advisors reaching the same conclusion is still not external evidence.

### Majority is not proof

The chairman can reject the majority view when a minority argument has stronger reasoning.

Agreement can also reflect shared model bias, so consensus is treated as something to evaluate — not proof by itself.

### One-round cap

Round 2 is the final additional reasoning pass.

If the remaining uncertainty requires new external evidence rather than more reasoning, the council stops and reports what evidence is needed. There is no automatic third reasoning round.

---

## When To Use It

LLM Council is useful when **being wrong is expensive** and the decision contains genuine uncertainty or tradeoffs.

### Good use cases

- Choosing between competing strategies or plans
- Product, pricing, or positioning decisions
- Evaluating whether a proposed approach is worth pursuing
- Pressure-testing a technical or architectural decision
- Reviewing a plan or draft where the consequences matter
- Debugging or diagnosis where multiple root causes are plausible
- Decisions where you want competing perspectives before committing

### Skip the council for

- Factual questions with a straightforward answer
- Simple yes/no questions
- Routine writing or creation tasks
- Summaries and transformations
- Casual decisions with little meaningful tradeoff

The council is intentionally selective. More reasoning is not automatically better reasoning.

---

## Trigger Phrases

Explicit triggers include:

- `council this`
- `run the council`
- `war room this`
- `pressure-test this`
- `stress-test this`
- `debate this`

It can also trigger for genuine decisions with meaningful tradeoffs, for example:

- "Should I choose X or Y?"
- "Which option is stronger?"
- "What would you do?"
- "Is this the right move?"
- "Validate this."
- "Get multiple perspectives."
- "I can't decide."
- "I'm torn between these options."

A casual "should I..." question without a real decision or meaningful tradeoff does not automatically trigger the council.

---

## Example

> **council this:** I'm considering replacing the current architecture with approach B. It would take two weeks to migrate, but could reduce long-term complexity. Should I make the switch?

The council does not simply answer yes or no.

It pressure-tests:

- what could make the migration fail,
- whether the underlying problem actually requires the migration,
- what upside may be overlooked,
- what an outside observer sees without the surrounding assumptions,
- what is realistically executable,
- where the advisors disagree,
- and whether the remaining uncertainty can actually be resolved.

The final output tells you what the council recommends, how confident it is, what the main uncertainty is, and what evidence or change would alter the decision.

---

## Structure

```
llm-council/
├── SKILL.md
├── README.md
├── LICENSE
└── references/
    ├── advisors.md
    ├── peer-review.md
    ├── chairman.md
    └── round-2.md
```

The implementation keeps the main workflow in `SKILL.md` and separates each council stage into focused reference files.

---

## Installation

Clone the repository into your agent's skills directory:

```bash
git clone https://github.com/SanyamKhaiwal/llm-council.git ~/.claude/skills/llm-council
```

Or copy the repository contents into the corresponding skills directory supported by your agent.

The skill is designed around sub-agent orchestration and works best in an environment that supports multiple independent agent passes.

---

## Design Principles

- **Independent first-pass reasoning** — advisors should not anchor on each other's conclusions.
- **Different reasoning lenses** — each advisor is given a distinct role.
- **Anonymous peer review** — critique the reasoning without relying on author identity.
- **Reduced-context outsider** — deliberately preserve one perspective outside the main framing.
- **Evidence discipline** — reasoning is not treated as external evidence.
- **Chairman gate** — every run must pass through a `FINAL`, `ROUND_2`, or `INSUFFICIENT` decision.
- **Single Round 2 cap** — additional reasoning is limited to one pass.
- **Legitimate uncertainty** — the system is allowed to conclude that the information is insufficient.
- **No confidence carryover** — a second round must earn its conclusion again.
- **Majority is not proof** — the strongest reasoning can come from a minority position.

---

## Credit

- Methodology inspired by [Andrej Karpathy's LLM Council](https://x.com/karpathy/status/1962263486196867115).
- This implementation adapts the council concept into an installable agent skill with structured advisor roles, anonymous peer review, chairman gating, and explicit uncertainty handling.

---

## License

MIT — see [LICENSE](LICENSE).
