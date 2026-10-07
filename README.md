# LLM Council

### Before you trust the answer, make it survive the argument.

One AI answer can sound convincing and still be wrong.

**LLM Council** makes the model challenge its own reasoning through:

**5 independent perspectives → anonymous peer review → chairman verdict**

It can recommend.

It can ask you for one missing fact.

Or it can tell you **there isn't enough information to decide.**

> Inspired by [Andrej Karpathy's LLM Council](https://x.com/karpathy/status/1962263486196867115).

---

## The Problem

LLMs are extremely good at producing answers that **sound right**.

That's useful until the decision actually matters.

Ask a model:

> "Which architecture should I choose?"

You might get a confident answer.

Ask again with slightly different wording, and you might get a confident answer in the opposite direction.

The problem isn't always intelligence.

It's that **one reasoning path doesn't expose its own blind spots very well.**

LLM Council takes a different approach.

Instead of asking for another answer, it makes the reasoning **argue with itself**.

---

## The Idea

```text
                         YOUR QUESTION
                              │
                              ▼
                    ┌───────────────────┐
                    │   5 ADVISORS       │
                    │  reason separately │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ ANONYMOUS REVIEW   │
                    │  break the logic   │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │     CHAIRMAN      │
                    │ synthesize + gate │
                    └─────────┬─────────┘
                              │
              ┌───────────────┼────────────────┐
              ▼               ▼                ▼
            FINAL          CLARIFY        INSUFFICIENT
