# LLM Council

**Make an AI decision survive an argument before you act on it, at a cost that matches the question.**

> Inspired by [Andrej Karpathy's LLM Council](https://github.com/karpathy/llm-council).

---

## The problem

You ask an AI an important question and get one confident, well-written answer. You can't tell whether it's right.

- **It sounds equally sure when it's wrong.** Rephrase the question and you can get an equally convincing answer the other way.
- **It states guesses as facts.** "That library is maintained." "That lock only blocks writes." Nobody checked.
- **Multi-agent councils fix this, but they're expensive.** Running five advisors and five reviewers on every question costs about 11 model calls, whether you're choosing a notes app or handling a production outage.

## The fix

LLM Council runs your decision through advisors with deliberately different viewpoints, checks the facts they depend on, has their reasoning attacked anonymously when that's worth paying for, and lets a chairman decide whether the result is solid enough to act on.

It **sizes itself to the question**: trivial questions cost nothing extra, and only high-stakes decisions get the full council.

---

## Results

Real test runs, compared with a fixed 11-call council:

| Question | Tier | Calls | Tokens | What the council caught |
|---|---|---|---|---|
| `map` vs a for loop on 10 items | Direct | **0** | ~0 | Nothing to debate, so it answered directly |
| Where to store a CLI tool's API key | Quick | **4** | ~175k (**−60%**) | Checked that the usual keychain library has been archived since 2022 |
| Redux migration for a 5-dev team | Standard | **7** | ~290k | A half-finished migration to one option leaves three state systems instead of one |
| Live payments outage, sale in 2 hours | Full | **7** | ~300k (**−30%**) | Turn the flag off *before* cancelling the index build, and check build progress first. Both changed the plan |
| Audit-trail design, advisors deadlocked | Standard + Round 2 | **+5** | | Found a hybrid option nobody had examined, which became the verdict |
| AI health feature with open legal questions | Standard | **+1** | | Researched the facts, then honestly said a lawyer is still needed, and why |

In the Full-tier outage run, reviewers found risks that all four advisors missed. In the Quick runs, no review was needed and none was paid for.

---

## Quick start

**Claude Code:** copy this folder to `~/.claude/skills/llm-council/`, then ask:

```text
council this: should we move our enterprise tenants to a dedicated EU database
or add row-level security to the shared one? 6 engineers, deal due in 6 weeks.
```

**Other LLM tools:** load `SKILL.md` as instructions. The reference files are read only when each stage needs them, and no specific model is required.

Trigger phrases: `council this` · `run the council` · `pressure-test this` · `stress-test this` · `war room this` · `debate this`. It also triggers on real decisions such as "should I X or Y?" or "I'm torn between…".

---

## What you get

**In chat**, a verdict you can read in under two minutes:

```markdown
## Verdict: Turn the flag off and freeze migrations, then cancel the blocking
index build. Roll back the app only if latency stays high.

**Confidence:** Medium. Postgres docs confirm the build blocks all writes,
but 3% errors and 85% CPU suggest a second cause.

### Why · ### Watch out for · ### What would change this · ### Next step
```

**Saved to `council-reports/`**, a decision record with the question, the verdict, the options considered and why each lost, the key assumptions, an implementation plan, checkpoints, and **sources**.

---

## How it works

```text
 Question
    │
    ▼
 Gate A ── size it ─────────► Direct: answer now (0 calls)
    │
    ▼
 Evidence pass ─── look up the facts it depends on (files, then web)
    │
    ▼
 Advisors (3-5, in parallel, independent)
    │
    ▼
 Gate B ── worth reviewing? ─► no: chairman critiques the answers itself
    │ yes
    ▼
 Anonymous peer review (1-3, each with a different focus)
    │
    ▼
 Chairman ──► FINAL · RESEARCH · CLARIFY · ROUND_2 · INSUFFICIENT
```

### Sizing: Gate A

The question is scored 0–2 on **stakes, reversibility, complexity and uncertainty**.

| Score | Tier | Advisors | Calls |
|---|---|---|---|
| 0–1 | **Direct** | none; it answers directly and offers a council | 0 |
| 2–3 | **Quick** | the core three | 4–5 |
| 4–5 | **Standard** | the core three, plus one seat that fits | 5–8 |
| 6–8 | **Full** | the core three, plus every seat that fits | 7–9 |

Material security, legal, data-loss or safety exposure never goes below Standard. Saying "quick council" or "full council" overrides the score.

### Review: Gate B

After the advisors answer, the council checks for reasons to pay for review:

| Signal | Example |
|---|---|
| **Split** | Advisors recommend options that rule each other out |
| **Load-bearing assumption** | The verdict depends on an unchecked "assuming X" |
| **Unanswered risk** | A serious risk was raised and nobody addressed it |
| **High stakes** | Expensive or irreversible if wrong |

No signals means 0 reviewers. More signals add reviewers, up to 3. High-stakes questions always get at least 2, because advisors running on the same model can share blind spots.

### The advisors

| Advisor | Asks | Seated |
|---|---|---|
| **Contrarian** | What fails, and which flaw is fatal? | Always |
| **First Principles** | Are we solving the right problem? | Always |
| **Executor** | What do we do Monday morning? | Always |
| **Expansionist** | What upside is everyone missing? | For growth and strategy questions |
| **Outsider** | What's confusing to someone with no context? | For anything other people will read or use |

The Outsider sees only your raw question, with no context and no evidence, so its fresh read stays fresh.

### The chairman's gates

| Gate | When | What happens |
|---|---|---|
| `FINAL` | **The default.** The reasoning supports a choice | Verdict: **Recommendation**, or **Conditional** if it hinges on a fact you can test |
| `RESEARCH` | It hinges on a fact that can be looked up | Up to 3 lookups, then the chairman re-runs (+1 call) |
| `CLARIFY` | It hinges on your own goals or situation | One question to you, then the chairman re-runs (+1 call) |
| `ROUND_2` | **Rare.** No option has a defensible basis, and one question settles it | 3 fresh advisors and 1 reviewer (+5 calls), once only |
| `INSUFFICIENT` | The missing fact can't be found | Names what's missing, gives the lean and the next action |

No Round 3, no endless interviews, and no confidence it hasn't earned.

---

## Evidence rules

| Rule | Why |
|---|---|
| Open at least one primary source per question (official docs, vendor pages, regulations, your own files) | Blog summaries are leads, not evidence |
| Rate every finding: Confirmed, Partial, Conflicting or Not found | "Not found" is an honest result |
| Never invent a URL, quote or date | A citation has to be real to be worth anything |
| Keep private details out of web queries | Your data stays yours |
| A confirmed source beats an advisor's claim | Reasoning isn't evidence |

---

## When to use it

| ✅ Good fit | ❌ Poor fit |
|---|---|
| Architecture and technical choices | Pure lookups ("what does X cost?") |
| Incidents with several possible remedies | Definitions, summaries, translations |
| Product, pricing and strategy calls | Questions with one right answer |
| Career decisions with real tradeoffs | Writing code: it decides and plans, but doesn't implement |
| Plans and drafts you want stress-tested | Low-stakes choices: the Direct tier handles these |

---

## Limits

| Limit | What it means |
|---|---|
| **One model, many viewpoints** | Advisors can share a model's blind spots. Review and evidence reduce this but don't remove it |
| **Sizing is a judgment call** | Two runs can occasionally pick different tiers |
| **Evidence depends on your tools** | Without web search, external facts stay unchecked, and the report says so |
| **Not legal, medical or financial advice** | Evidence shows what the rules say, not how they apply to you |

**Tested:** every tier and every gate, with sub-agents in Claude Code. **Not yet tested:** other LLMs, the mode without sub-agents, trigger accuracy, and consistency across runs.

---

## Repository

```text
llm-council/
├── SKILL.md            # the workflow the model follows
└── references/
    ├── triage.md       # Gate A sizing, Gate B review, call budget
    ├── evidence.md     # evidence pass, RESEARCH gate, source rules
    ├── advisors.md     # the five viewpoints and their prompts
    ├── peer-review.md  # anonymization, focused reviewers
    ├── chairman.md     # synthesis, gates, verdict types
    ├── report.md       # chat verdict and saved decision record
    └── round-2.md      # the rare second pass
```

MIT License · see [LICENSE](LICENSE)

## Contributing and security

Contributions and reproducible bug reports are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for the contribution workflow and [SECURITY.md](SECURITY.md) for private security-reporting guidance.
