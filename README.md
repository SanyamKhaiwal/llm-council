# LLM Council

A multi-agent decision-making skill for questions where **being wrong is costly** and the answer is not obvious.

It uses **five independent advisors, anonymous peer review, and a chairman** to pressure-test a decision before giving a verdict.

Inspired by [Andrej Karpathy's LLM Council](https://x.com/karpathy/status/1962263486196867115).

> **Core idea:** the council is optimized for a reliable decision, not for producing an answer at any cost.

---

## What It Can Do

### 1. Get five genuinely different perspectives

Five advisors analyze the problem independently:

| Advisor | Main question |
|---|---|
| **Contrarian** | What could go wrong? What is most likely to fail? |
| **First Principles** | Are we solving the right problem at all? |
| **Expansionist** | What upside or opportunity are we overlooking? |
| **Outsider** | What does this look like to someone with no insider context? |
| **Executor** | Can this actually be done, and what should happen first? |

They run **in parallel**, so one advisor does not anchor the others.

---

### 2. Separate the question from the answer

Before the advisors run, the council:

- reads relevant user and workspace context,
- identifies the actual decision,
- frames the question neutrally,
- avoids embedding a preferred answer into the prompt.

This reduces the risk of getting a different answer simply because the question was phrased differently.

---

### 3. Use an independent Outsider

The Outsider deliberately receives only the user's raw question.

It does **not** receive workspace context, memory, past results, or the council's framing.

This helps catch:

- hidden assumptions,
- confusing explanations,
- insider bias,
- missing context,
- alternatives that become invisible after heavy framing.

---

### 4. Critique the advisors anonymously

After the first pass, all five responses are randomly shuffled and anonymized.

Five independent reviewers then examine them without knowing which advisor wrote which response.

Each reviewer looks for:

1. **Strongest response** — what is its strongest claim, what supports it, what assumption it relies on, and what would falsify it?
2. **Biggest blind spot** — what important gap could damage the decision?
3. **What all five missed** — what should the council consider that nobody raised?

This makes the system more than simply asking five models for five opinions.

---

### 5. Detect agreement that should not be trusted

The chairman evaluates:

- where the advisors agree,
- where they disagree,
- blind spots,
- critical assumptions,
- evidence quality,
- root causes and causal chains,
- whether a minority argument is actually stronger than the majority.

**Consensus is not treated as proof.**

Five advisors can share the same model bias, so agreement is treated as something to examine rather than automatically trust.

---

### 6. Track what is known vs assumed

The chairman separates important claims into categories such as:

- **Verified Fact**
- **User-Stated Fact**
- **Evidence**
- **Inference**
- **Assumption**
- **Unknown**

This helps prevent an assumption from silently turning into a "fact" during a long reasoning chain.

---

### 7. Perform root-cause analysis

For debugging and diagnosis problems, the chairman distinguishes between:

- the symptom,
- the mechanism causing it,
- contributing factors,
- the proposed root cause.

A proposed root cause should be supported by a causal chain and include evidence or a way it could be falsified.

---

### 8. Ask for clarification when the decision depends on one missing user fact

The council can stop and ask **one concise clarification question** when the unresolved crux depends on information only the user can provide.

After the user answers, the council restarts the reasoning cycle with that information included.

It does not keep asking questions indefinitely.

---

### 9. Run one focused second reasoning round

If the remaining uncertainty is a **single question that reasoning can resolve**, the chairman can trigger Round 2.

Round 2:

- uses fresh advisors,
- does not show them the Round 1 verdict,
- focuses only on the unresolved crux,
- performs another anonymous peer review,
- cannot trigger a third round.

If the uncertainty requires external evidence rather than more reasoning, Round 2 is not used.

---

### 10. Know when to stop

The council can explicitly return:

**INSUFFICIENT INFORMATION**

when the available reasoning cannot reliably settle the decision.

It identifies:

- what is unknown,
- why reasoning cannot resolve it,
- what evidence or information would change the decision.

The system is deliberately allowed to say **"we don't know."**

---

## How It Works

```
User Question
     |
     v
Frame the Decision
     |
     +-------------------------------+
     |                               |
     v                               v
4 Context-Aware Advisors        Outsider
     |                         Raw Question Only
     +---------------+---------------+
                     |
                     v
             Anonymous Peer Review
                     |
                     v
                 Chairman
                     |
          +----------+----------+-----------+
          |          |          |           |
        FINAL     CLARIFY    ROUND_2   INSUFFICIENT
          |          |          |           |
          |          |          v           |
          |          |    Fresh reasoning   |
          |          |    + peer review     |
          |          |          |            |
          |          +----> Restart         |
          |                                 |
          +---------------+-----------------+
                          |
                          v
                       Verdict
```

---

## The Chairman's Verdict

The final result can be:

### Recommendation
The council has enough support to recommend a course of action.

### Conditional Recommendation
The council recommends an action, but the decision depends on a specific assumption that should be checked.

### Insufficient Information
The available information is not enough to make a reliable recommendation.

The final response also explains:

- where the council agrees,
- where it clashes,
- critical assumptions,
- evidence quality,
- blind spots,
- what could change the verdict,
- the **one thing to do first**.

---

## When To Use It

Use the council for decisions with **meaningful uncertainty, competing tradeoffs, or expensive failure**.

Good examples:

- Choosing between technical architectures
- Evaluating a product or business strategy
- Deciding whether to pursue an idea
- Career or project decisions with significant tradeoffs
- Debugging where several root causes are plausible
- Reviewing an important plan
- Pressure-testing an argument or proposal
- Comparing multiple approaches before committing

### Don't use it for

- Simple factual questions
- Straightforward calculations
- Routine writing
- Summaries
- Simple transformations
- Low-stakes decisions

More agents are not automatically better. The council is intentionally selective.

---

## Trigger Phrases

Explicit triggers include:

- `council this`
- `run the council`
- `war room this`
- `pressure-test this`
- `stress-test this`
- `debate this`

It can also trigger for genuine decisions such as:

- "Should I choose X or Y?"
- "Which option is stronger?"
- "What would you do?"
- "Is this the right move?"
- "Validate this."
- "Get multiple perspectives."
- "I can't decide."

A casual question without a meaningful decision or tradeoff does not automatically require the council.

---

## What Makes It Different

### It is not just "ask five agents"

The process adds several layers of protection:

- independent first-pass reasoning,
- deliberately different reasoning lenses,
- a reduced-context outsider,
- anonymous peer review,
- explicit assumption tracking,
- evidence-vs-reasoning separation,
- root-cause analysis,
- chairman gating,
- user clarification when needed,
- one focused second reasoning round,
- a legitimate insufficient-information outcome.

### It does not manufacture certainty

The council does **not** treat:

- confidence as evidence,
- consensus as proof,
- reasoning as external evidence,
- a majority vote as automatically correct.

A minority position can win if its reasoning is stronger.

---

## Example

```
council this:

I'm considering replacing our current architecture with approach B.
Migration would take two weeks but may reduce long-term complexity.
Should I make the switch?
```

The council will examine:

- what could make the migration fail,
- whether the migration actually solves the underlying problem,
- potential upside,
- implementation difficulty,
- what an outsider sees,
- disagreements between advisors,
- hidden assumptions,
- and whether the remaining uncertainty can be resolved.

The output is a **decision with reasoning**, not just a vote.

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

- `SKILL.md` — main orchestration and workflow
- `advisors.md` — five advisor roles and prompts
- `peer-review.md` — anonymization and review process
- `chairman.md` — synthesis, gating, assumptions, evidence, and verdict
- `round-2.md` — controlled second reasoning pass

---

## Installation

Clone the repository into your agent's skills directory:

```bash
git clone https://github.com/SanyamKhaiwal/llm-council.git ~/.claude/skills/llm-council
```

Or copy the repository contents into the skills directory supported by your agent.

The skill works best in an environment that supports multiple independent agent/sub-agent passes.

---

## Design Principles

- **Independent reasoning** — advisors do not see each other's first-pass answers.
- **Different lenses** — each advisor is deliberately optimized for a different failure mode.
- **Fresh outsider perspective** — one advisor sees only the raw question.
- **Anonymous criticism** — reviewers judge reasoning rather than advisor identity.
- **Evidence discipline** — reasoning and evidence are kept separate.
- **Explicit uncertainty** — the council can conclude that the information is insufficient.
- **Controlled escalation** — clarification or one Round 2 pass is used only when justified.
- **No confidence carryover** — Round 2 must independently earn its conclusion.
- **Minority can win** — the strongest argument matters more than vote count.
- **No third round** — uncertainty cannot be hidden behind endless deliberation.

---

## Credit

- Methodology inspired by [Andrej Karpathy's LLM Council](https://x.com/karpathy/status/1962263486196867115).
- This implementation adapts the council concept into an installable agent skill with structured advisor roles, anonymous peer review, chairman gating, root-cause analysis, and explicit uncertainty handling.

## License

MIT — see [LICENSE](LICENSE).
