# Triage

The council's cost is driven by how many sub-agents it runs, and every sub-agent pays a fixed setup cost before it writes a word. Most questions don't need all of them. Triage spends effort where it changes the answer and nowhere else.

There are two gates. The orchestrator applies both itself, without spawning anything:

- **Gate A, sizing** (before the advisors): how many advisors, and which ones.
- **Gate B, review** (after the advisors): whether peer review is worth running, and with how many reviewers.

Gate A is a prediction made before any analysis exists. Gate B is a check against what the advisors actually said. Gate B is what decides whether a question deserves review.

## Contents

- Gate A: sizing
- Choosing advisors
- Gate B: review
- Announcing the mode
- Call budget

## Gate A: sizing

Score the framed question 0, 1 or 2 on each factor. Score from the facts in the question, not from how important it sounds.

| Factor | 0 | 1 | 2 |
|---|---|---|---|
| **Stakes** | Trivial cost if wrong | Meaningful cost if wrong | Large money, security, legal, health, or reputation exposure |
| **Reversibility** | Undo within a day | Weeks of work to undo | Hard or impossible to undo |
| **Complexity** | One main factor | Several interacting factors | Many systems, people, or domains; hidden dependencies likely |
| **Uncertainty** | One option is plausibly clear | A real tradeoff | Options are close, or key facts are unknown |

Add the scores (0 to 8) and pick a tier:

| Total | Tier | Advisors |
|---|---|---|
| 0–1 | **Direct** | None. Answer the question yourself in a few sentences, then offer: "Want me to run the council on this?" |
| 2–3 | **Quick** | 3: the core three |
| 4–5 | **Standard** | 4: the core three plus one situational seat |
| 6–8 | **Full** | 4 or 5: the core three, plus every situational seat that fits |

**Floors.** These override the total:

- If the user used a mandatory trigger ("council this", "run the council", and so on), the minimum tier is Quick. They asked for a council, so don't answer directly.
- If any factor scored 2 for Stakes or Reversibility, the minimum tier is Standard.
- If getting it wrong could cause a **material** security breach, loss of data other people depend on, legal or regulatory exposure, or harm to someone's safety, the minimum tier is Standard. Merely touching the topic doesn't count: where to keep one personal API key is not a material security decision; how customer data is isolated is.

**User overrides win.** "Quick council" or "full council" sets the tier directly. "Don't skip the review" forces Gate B to run at least two reviewers.

Score once. Don't re-score because of what the advisors later say. Gate B handles that.

## Choosing advisors

**The core three run in every tier:** the Contrarian, the First Principles Thinker and the Executor. Between them they cover what fails, whether this is the right problem, and what to do first.

**The situational seat (Standard)** is the one that fits the question best:

- **The Expansionist** when the decision is about opportunity, growth, strategy, pricing, or what to build.
- **The Outsider** when the subject is something other people will see or read: messaging, a pitch, a product, a landing page, a document. Also when the user is deep in jargon and might be blind to it.
- If both fit, prefer the Outsider for audience-facing questions and the Expansionist otherwise. Technical and operational decisions usually take the Outsider only if the question is hard to follow without context.

**Full** seats the core three plus each situational seat that genuinely fits the question: both if both fit, one if only one does. Full always seats at least 4: if neither situational seat clearly fits, seat the closer fit by the Standard rule above. Beyond that, don't seat an advisor just to fill the tier. In an urgent operational decision (an incident, a deadline in hours), the Expansionist usually doesn't fit.

## Gate B: review

Run this after the advisors return and before peer review. Read the advisor responses and check four signals:

| Signal | It's present when |
|---|---|
| **S1. Split** | Advisors recommend different primary options, or recommend actions where doing one rules out another (for example "rebuild the index before peak" vs "no rebuild until after"), or one advisor argues the question itself is wrong in a way the others ignored. Differences in emphasis, in the order of compatible steps, or extra suggestions on top of the same choice are **not** a split. |
| **S2. Load-bearing assumption** | The leading recommendation depends on an "assuming X" or an unstated fact that, if false, would change the choice. |
| **S3. Unanswered serious risk** | An advisor names a risk that could be fatal or very costly, and no other advisor addresses it. |
| **S4. High stakes** | Gate A scored Stakes or Reversibility at 2, or a safety, security, legal or data-loss floor applied. |

Reviewer count:

| Signals present | Reviewers |
|---|---|
| None | **0.** Skip peer review. The chairman does the self-critique in `chairman.md`. |
| One of S1, S2, S3 only | **1** |
| Two signals, or S4 alone | **2** |
| Three or more, or S4 with S1 | **3** (the maximum) |

Unanimous advisors on a low-stakes question get no review: more readers won't find what three to five agreeing lenses missed often enough to pay for itself. A high-stakes question always gets at least two reviewers, even when advisors agree, because agreement among instances of one model can be shared blind spots. Those reviewers are the ones most likely to find what all the advisors missed.

Record the signals you found in one line for the audit notes, for example `Gate B: S2, S4 → 2 reviewers`.

## Announcing the mode

Before spawning the advisors, tell the user in one line which tier you chose and why, for example:

> Council mode: Quick (3 advisors, review only if they disagree). Low stakes, easy to reverse.

After Gate B, add one line only if it changes the plan, for example:

> Advisors split on the main option. Adding 2 reviewers.

## Call budget

Sub-agent calls per council, before any `CLARIFY` restart:

| Tier | Advisors | Reviewers | Chairman | Total |
|---|---|---|---|---|
| Direct | 0 | 0 | 0 | 0 |
| Quick | 3 | 0–1 | 1 | 4–5 |
| Standard | 4 | 0–3 | 1 | 5–8 |
| Full | 4–5 | 2–3 | 1 | 7–9 |
| Round 2 (Standard and Full only) | +3 | +1 | +1 | +5 |

Without sub-agents, the same counts apply as separate sequential passes.

Evidence gathering (`evidence.md`) adds tool calls, not agent calls: up to 3, 5 or 8 looked-up questions for Quick, Standard and Full, plus at most one `RESEARCH` round, which costs one extra chairman call.
