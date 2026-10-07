# LLM Council

### Before you trust the answer, make it survive the argument.

One model can give you a convincing answer.

LLM Council makes that answer go through **five different reasoning perspectives, anonymous peer review, and a chairman** before deciding whether it is actually strong enough to act on.

It can recommend.

It can ask for one missing fact.

Or it can tell you **there isn't enough information to decide.**

> Inspired by [Andrej Karpathy's LLM Council](https://x.com/karpathy/status/1962263486196867115).

---

## Why This Exists

LLMs are extremely good at producing convincing explanations.

That's also the problem.

For an important decision, asking a model once can produce a polished answer without exposing the assumptions underneath it. Ask the same question differently and you can sometimes get an equally convincing answer in the opposite direction.

LLM Council is designed to make that harder.

Instead of simply asking the model for another answer, it creates **independent reasoning paths**, has other agents **critique those paths anonymously**, and then gives everything to a chairman whose job is to determine whether the reasoning actually supports a decision.

It is **not a voting system**.

It is not designed to manufacture certainty.

Its goal is to find the strongest decision the available information supports — and explicitly say when the information isn't enough.

---

## How It Works

| Stage | What happens | Why it exists |
|---|---|---|
| **Frame** | The user's question is turned into a decision-ready problem. | Prevents the council from reasoning about an ambiguous question. |
| **Advisors** | Five advisors independently analyze the problem from different perspectives. | Creates multiple reasoning paths before anyone can anchor on another answer. |
| **Peer Review** | The five responses are shuffled and anonymously reviewed by five reviewers. | Forces the reasoning to survive criticism rather than simply count opinions. |
| **Chairman** | The chairman synthesizes the arguments, disagreements, assumptions, evidence, and blind spots. | Produces a decision based on reasoning quality rather than majority vote. |
| **Gate** | The chairman decides between `FINAL`, `CLARIFY`, `ROUND_2`, or `INSUFFICIENT`. | Prevents the system from forcing a conclusion when it shouldn't. |
| **Round 2** | If one reasoning-resolvable crux remains, fresh advisors investigate it using Round 1's structured findings without inheriting its conclusion. | Gives genuine reasoning deadlocks one additional attempt without creating endless deliberation. |

There is **no Round 3**.

If the uncertainty depends on external evidence rather than reasoning, the council stops.

---

## The Five Advisors

Each advisor has a different job. They reason independently before seeing the other responses.

| Advisor | Role | The question they're trying to answer |
|---|---|---|
| **Contrarian** | Hunts for failure modes, bad assumptions, and risks. | *"What's wrong with this, and what could actually kill it?"* |
| **First Principles** | Strips the problem down and questions the assumptions behind it. | *"Are we even solving the right problem?"* |
| **Expansionist** | Looks for upside, opportunities, and possibilities others might miss. | *"What happens if this works better than expected?"* |
| **Outsider** | Looks at the raw question without insider context, history, or workspace knowledge. | *"What would a smart stranger see here?"* |
| **Executor** | Focuses on feasibility, blockers, first steps, and practical execution. | *"Cool idea. How do we actually ship it?"* |

The point isn't that one advisor is **"the correct one."**

The point is that different failure modes get a chance to speak before the decision is made.

---

## The Outsider

The Outsider deliberately receives less context than the other advisors.

It gets the raw user question without:

- workspace history,
- previous council results,
- insider context,
- or the reasoning already produced by the other advisors.

This creates a useful counterweight.

The Outsider can catch assumptions that become invisible once everyone already knows the backstory:

- confusing framing,
- missing context,
- hidden assumptions,
- unexplored alternatives,
- or a problem with the question itself.

It isn't necessarily more informed.

It is deliberately **less informed**.

---

## Anonymous Peer Review

The five advisor responses are shuffled before review.

Reviewers don't know which advisor produced which response.

Each reviewer examines the reasoning for:

- the strongest argument,
- the weakest argument,
- unsupported assumptions,
- important disagreements,
- damaging blind spots,
- and something **all five advisors missed**.

This is important because agreement alone isn't particularly impressive.

Five advisors reaching the same conclusion may simply mean they share the same assumption.

The peer-review stage gives the reasoning another chance to fail **before the chairman accepts it**.

---

## The Chairman

The chairman does not simply count votes.

It evaluates the quality and support behind the arguments.

### Agreement

What conclusions survive across multiple independent perspectives?

### Disagreement

Where do the advisors genuinely differ, and what causes the disagreement?

### Blind Spots

What important possibility did the council fail to consider?

### Critical Assumptions

Which assumptions does the decision actually depend on?

Important claims are classified as:

| Classification | Meaning |
|---|---|
| **Verified Fact** | Supported by available evidence. |
| **User-Stated Fact** | Provided directly by the user. |
| **Evidence** | External or supplied evidence supporting the claim. |
| **Inference** | A conclusion derived from available information. |
| **Assumption** | Something being treated as true without sufficient support. |
| **Unknown** | Something the council cannot currently establish. |

This distinction matters because:

> **Reasoning is not evidence.**

A beautifully reasoned conclusion built on an unverified assumption is still uncertain.

### Evidence Quality

The chairman separates what is actually supported from what merely sounds plausible.

If the decision depends on an external fact that the council cannot establish, reasoning harder does not solve the problem.

### Root-Cause Analysis

For debugging and diagnostic problems, the chairman looks beyond the visible symptom.

A proposed root cause should explain the causal chain:

**Symptom → Mechanism → Contributing Factors → Root Cause**

The root cause should also have some form of falsification evidence: something that could demonstrate that the proposed cause is wrong.

---

## Majority Does Not Mean Correct

LLM Council is deliberately **not a majority-voting system**.

If four advisors support A and one advisor identifies a fundamental flaw in the reasoning behind A, the minority argument can win.

The chairman evaluates the argument, not the vote count.

The goal is not:

> "What did most agents say?"

It is:

> **"Which conclusion is best supported after the reasoning has been challenged?"**

---

## Knowing When Not to Answer

One of the most important properties of the council is that **a forced answer is not always a successful answer**.

The chairman has four possible gates.

### `FINAL`

There is enough support to make a recommendation.

The council provides the verdict, reasoning, confidence, main uncertainty, and what could change the decision.

### `CLARIFY`

The decision depends on one user-specific fact that only the user can provide.

The council asks **one concise clarification question**, incorporates the answer, and restarts the reasoning cycle.

It does not turn into an endless interview.

### `ROUND_2`

There is one specific unresolved crux that:

1. materially affects the decision,
2. cannot be resolved from the current reasoning,
3. and can potentially be resolved through another reasoning pass.

Round 2 uses fresh advisors and fresh peer review.

Round 2 carries forward a structured handoff of useful Round 1 findings, while withholding the first round's verdict, confidence, and interim lean. This preserves useful context without anchoring the second round to the first round's judgment.

### `INSUFFICIENT`

The uncertainty cannot be resolved through reasoning alone.

The missing piece may be:

- external evidence,
- an experiment,
- a benchmark,
- a measurement,
- domain expertise,
- or some other unavailable fact.

The council stops rather than manufacturing confidence.

---

## What Makes Round 2 Different?

Round 2 is deliberately constrained.

It is **not**:

> "Ask the same agents again until they agree."

Instead:

- the unresolved crux is isolated,
- fresh advisors investigate it,
- fresh peer reviewers attack the new reasoning,
- the first round's confidence is not carried forward,
- and the chairman makes a final decision.

If uncertainty remains after Round 2, the council stops.

**There is no Round 3.**

---

## Where LLM Council Is Useful

The council is particularly useful when:

- there are multiple plausible answers,
- tradeoffs are real,
- assumptions matter,
- the decision has meaningful consequences,
- and you already have enough information to reason about the problem.

### Good use cases

| Situation | Why the council helps |
|---|---|
| **Software architecture** | Exposes long-term tradeoffs and hidden assumptions. |
| **Technical decisions** | Forces alternatives and failure modes into the discussion. |
| **Debugging** | Separates symptoms, mechanisms, contributing factors, and root causes. |
| **Product decisions** | Pressure-tests competing strategies. |
| **Career decisions** | Forces multiple perspectives on tradeoffs and uncertainty. |
| **Project planning** | Identifies risks, blockers, and assumptions before execution. |
| **Business strategy** | Challenges both the downside and upside of a proposed move. |

The sweet spot is:

> **A high-impact decision where you have enough context to reason, but you're not sure whether your reasoning is missing something important.**

---

## When You Shouldn't Use It

LLM Council is intentionally **not** the answer to every question.

### Don't use it when the answer is simply a lookup.

For example:

- current API behavior,
- pricing,
- benchmarks,
- hardware specifications,
- library bugs,
- legal or regulatory rules,
- current documentation,
- or any other question where an external fact is missing.

Five agents reasoning harder cannot create a fact that none of them have.

**Get the evidence first. Then use the council to reason about what that evidence means.**

### Don't use it for trivial questions.

If you need a definition, calculation, straightforward coding answer, translation, summary, or routine writing task, a normal model response is usually better.

The council has real cost and latency.

Use it when the decision is worth that cost.

---

## Where It's Useful

LLM Council is most useful when the decision has **real tradeoffs and a meaningful cost to getting it wrong**.

| Use case | Why it fits |
|---|---|
| **Software architecture** | Multiple valid approaches with different long-term tradeoffs. |
| **Technical decisions** | Competing designs, implementation strategies, or engineering priorities. |
| **Debugging** | Several plausible causes where identifying the actual root cause matters. |
| **Product decisions** | Choosing between competing features, strategies, or investments. |
| **Career decisions** | Decisions involving multiple long-term tradeoffs rather than a single obvious answer. |
| **Project planning** | Pressure-testing plans, risks, assumptions, and execution constraints. |
| **Business strategy** | Evaluating competing opportunities where both upside and downside matter. |

The sweet spot is:

> **A high-impact decision where you have enough context to reason, but you're not sure whether your reasoning is missing something important.**

---

## Where It's Not Useful

LLM Council is intentionally **not** the answer to every question.

Don't use it when the answer is primarily an external fact, such as:

- current API behavior,
- pricing,
- benchmarks,
- hardware specifications,
- library bugs,
- legal or regulatory rules,
- current documentation,
- or anything else that requires information the council doesn't have.

Five agents reasoning harder cannot create a missing fact.

**Get the evidence first. Then use the council to reason about what that evidence means.**

It's also unnecessary for simple questions, calculations, definitions, summaries, translations, or straightforward coding problems.

The council has real cost and latency. Use it when the decision is worth that cost.

---

## Limitations

The council creates multiple reasoning paths, but it does **not** create five independent experts.

All five advisors may still be instances of the same underlying model. They can therefore share:

- the same blind spots,
- the same assumptions,
- the same hallucinations,
- and the same model-level limitations.

Peer review helps expose some of these problems, but it does not turn model-generated agreement into external evidence.

That's why the council is best thought of as a **reasoning amplifier**, not an oracle.

---

## Trigger Phrases

You can explicitly call the council with:

- `council this`
- `run the council`
- `war room this`
- `pressure-test this`
- `stress-test this`
- `debate this`

It can also be triggered naturally by genuine decision questions such as:

- "Should I choose X or Y?"
- "Which option is stronger?"
- "What would you do?"
- "Is this the right move?"
- "Validate this."
- "Get multiple perspectives."
- "I can't decide."

The trigger should represent a **real decision or reasoning problem**, not simply a request to make a normal answer longer.

---

## Repository Structure

```text
llm-council/
├── SKILL.md
├── README.md
├── LICENSE
└── references/
    ├── advisors.md
    ├── peer-review.md
    ├── chairman.md
    └── round-2.md
