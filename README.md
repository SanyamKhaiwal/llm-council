# LLM Council

A decision-making skill that makes an AI answer **argue with itself before asking you to trust it**.

Instead of getting one confident response, you get five independent takes, anonymous criticism, and a chairman that decides whether the reasoning is actually good enough to act on.

Inspired by [Andrej Karpathy's LLM Council](https://x.com/karpathy/status/1962263486196867115).

---

## Why this exists

AI is very good at producing convincing explanations.

That is also the problem.

For an important decision, asking the same model once can give you a polished answer without exposing the assumptions underneath it. Ask the question slightly differently and you can sometimes get an equally convincing answer in the opposite direction.

LLM Council is built to make that harder.

It is **not a voting system** and it is not designed to manufacture certainty. Its job is to find the strongest decision the available information supports — and say when the information isn't enough.

---

## The 5 Advisors

Each advisor has a different job. They run independently, so nobody gets to anchor the others.

| Advisor | Role | The question they're trying to answer |
|---|---|---|
| **Contrarian** | Hunts for failure modes, bad assumptions, and risks. | *"What's wrong with this, and what could actually kill it?"* |
| **First Principles** | Strips the problem down and questions the assumptions behind it. | *"Are we even solving the right problem?"* |
| **Expansionist** | Looks for upside, opportunities, and possibilities everyone else might miss. | *"What happens if this works better than expected?"* |
| **Outsider** | Looks at the raw question without insider context, history, or workspace knowledge. | *"What would a smart stranger see here?"* |
| **Executor** | Focuses on feasibility, first steps, blockers, and practical execution. | *"Cool idea. How do we actually ship it?"* |

The point isn't that one advisor is "the correct one."

The point is that **different failure modes get a chance to speak before the decision is made.**

### Then they criticize each other

The five responses are shuffled and anonymized.

Five separate reviewers read them without knowing who wrote what and look for:

- the strongest argument,
- the most damaging blind spot,
- unsupported assumptions,
- and something **all five missed**.

This is one of the most useful parts of the system. Agreement between five advisors is interesting; agreement after five reviewers have tried to break that reasoning is much more informative.

### Then the chairman has to make the call

The chairman doesn't simply count votes.

It looks at:

- where the advisors agree,
- where they disagree,
- what assumptions the decision depends on,
- what is actually supported by evidence,
- whether a minority argument is stronger,
- and, for debugging problems, whether the proposed root cause has a real causal chain.

It can also stop the process instead of forcing an answer.

---


## The part I care about most: knowing when not to answer

The council has three useful escape routes.

### **FINAL**

There is enough support to make a recommendation.

### **CLARIFY**

The decision hinges on one fact that only the user can provide.

The council asks one focused question, then starts the reasoning cycle again with the answer.

### **INSUFFICIENT**

The council cannot resolve the uncertainty with reasoning alone.

Instead of inventing confidence, it tells you what information or evidence is missing.

---

## One extra round, not endless deliberation

Sometimes the first council reaches a genuine reasoning deadlock.

If there is **one specific crux that reasoning can settle**, the chairman can send the problem through one fresh Round 2.

Round 2 doesn't get the first round's conclusion. The new advisors investigate the crux from scratch, followed by another anonymous review.

There is **no Round 3**.

If the missing piece is external evidence rather than reasoning, the council stops.

---

## What makes it useful

The council is particularly good at decisions where:

- there are multiple plausible answers,
- the tradeoffs are real,
- assumptions matter,
- and being wrong has a meaningful cost.

For example:

- choosing between software architectures,
- deciding whether a project or strategy is worth pursuing,
- reviewing an important technical or product decision,
- debugging a problem with several plausible root causes,
- pressure-testing a plan before committing to it,
- comparing two career or business options.

It is deliberately **not** meant for every question.

---

## Best use case

**A high-stakes decision where you already have enough context, but you're not sure whether your own reasoning is missing something.**

That is where the council earns its cost.

You give it the situation, constraints, options, and whatever evidence you already have. The council attacks the decision from several directions and tells you where the real uncertainty is.

For example:

> "I have two architectures. Both work. One is faster to ship, the other should be easier to maintain. Which should I commit to for the next two years?"

That is a good council question.

---

## Worst use case

**A question that can be answered by looking something up.**

If the answer depends on current pricing, a benchmark, a specification, a legal rule, a bug in a particular library version, or some other missing external fact, five agents reasoning harder will not create that fact.

Use evidence first. Use the council to reason about what the evidence means.

It is also overkill for simple questions. If you need a definition, a quick calculation, or a straightforward coding answer, just ask normally.

---

## Honest assessment

This system improves **reasoning quality**, but it does not magically create five independent experts.

All five advisors may still be instances of the same underlying model. That means the council can share the same blind spots, assumptions, or hallucinations.

The peer-review layer helps catch some of that, but it does not turn model-generated agreement into external evidence.

### My assessment

| Area | Assessment |
|---|---|
| **Decision quality** | Strong for ambiguous, high-impact decisions |
| **Reasoning depth** | Strong |
| **Bias / blind-spot detection** | Stronger than a single response |
| **Uncertainty handling** | Excellent |
| **Root-cause analysis** | Strong, especially when several causes are plausible |
| **Evidence handling** | Good — but depends on the evidence provided |
| **Independence** | Moderate — shared underlying model is still a limitation |
| **Cost / latency** | High |
| **Best value** | Decisions where a wrong call is expensive |
| **Worst value** | Simple questions or fact lookups |

### Bottom line

**For serious decisions, it is substantially more useful than simply asking the model once.**

But it is not a replacement for real evidence, domain expertise, experiments, or testing.

The council's biggest strength is not that it produces *more answers*.

It is that it creates more opportunities to discover **why the obvious answer might be wrong**.

---

## Trigger phrases

You can explicitly call it with:

- `council this`
- `run the council`
- `war room this`
- `pressure-test this`
- `stress-test this`
- `debate this`

It can also be triggered by genuine decision questions such as:

- "Should I choose X or Y?"
- "Which option is stronger?"
- "What would you do?"
- "Is this the right move?"
- "Validate this."
- "Get multiple perspectives."
- "I can't decide."

---

## Repository structure

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

- `SKILL.md` — main workflow and orchestration
- `advisors.md` — advisor roles and prompts
- `peer-review.md` — anonymization and peer review
- `chairman.md` — synthesis, gating, evidence, and verdict
- `round-2.md` — controlled second reasoning pass

---

## Installation

```bash
git clone https://github.com/SanyamKhaiwal/llm-council.git ~/.claude/skills/llm-council
```

Or copy the repository into the skills directory supported by your agent.

The skill works best in an environment that supports multiple independent agent/sub-agent passes.

---

## Credit

Inspired by [Andrej Karpathy's LLM Council](https://x.com/karpathy/status/1962263486196867115).

This implementation turns that idea into an installable skill with independent reasoning, anonymous peer review, chairman gating, explicit uncertainty handling, and a controlled second round.

## License

MIT — see [LICENSE](LICENSE).
