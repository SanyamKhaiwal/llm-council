# Peer Review

Peer review is what makes the council more than "ask five times." Reviewers read all five responses without knowing who wrote what, attack the reasoning, and surface what everyone missed. The chairman then has criticism to work with, not only five opinions.

## Contents

- Anonymization procedure
- Reviewers
- Reviewer prompt
- Handling the output

## Anonymization procedure

If reviewers know which advisor said what, they defer to a thinking style ("the Contrarian is usually right") instead of judging the reasoning. So:

1. Collect all five advisor responses.
2. Shuffle them with a genuinely random draw. Don't use a fixed order, and don't let the position of a response correlate with its advisor. Use code (for example Python's `random.shuffle`) when a shell is available.
3. Relabel the shuffled responses Response A through Response E.
4. Remove self-identifying labels from the text, such as "As the Contrarian..." or a header naming the advisor. Change nothing else. Keep the wording verbatim.
5. Record the letter-to-advisor mapping. It goes to the chairman and the transcript. **Reviewers never see it.**

Use one shuffle for the whole review round, so all five reviews refer to the same letters and the chairman can compare them directly. Run a new shuffle in Round 2.

## Reviewers

Run five independent reviewers in parallel. Reviewers are neutral. Don't give them an advisor persona, because a reviewer in a persona would judge by that lens instead of on the merits. Five runs give redundancy: when several reviewers independently flag the same blind spot, that is a stronger internal signal than one, but it is not independent external evidence.

Tell reviewers that some advisors worked from less context by design, without saying which. This keeps them from marking down a response for lacking background detail, and doesn't reveal the Outsider.

Reviewers judge claims and reasoning, not tone, length, or confidence. A confident, polished response can still rest on an unsupported assumption.

## Reviewer prompt

```
You are reviewing the outputs of an LLM Council. Five advisors independently
answered this question:

---
[framed question]
---

Some advisors worked from less context than others, by design. Don't penalize a
response for missing background detail. Judge what it says on its merits.

Here are the anonymized responses:

**Response A:**
[response]

**Response B:**
[response]

**Response C:**
[response]

**Response D:**
[response]

**Response E:**
[response]

Answer these three questions. Be specific and refer to responses by letter.
Judge the reasoning, not the tone or how confident it sounds.

1. STRONGEST RESPONSE. Pick one. Then give:
   - its strongest claim
   - the evidence or reasoning that supports that claim
   - the assumption underneath it
   - what would falsify it

2. BIGGEST BLIND SPOT. Pick the response with the most damaging gap. What is it
   missing? Give a counterexample or a missing consideration if you can.

3. WHAT ALL FIVE MISSED. What should the council consider that none of the five
responses raised?

Under 250 words. Be direct.
```

## Handling the output

- Give the chairman all five reviews with the mapping revealed.
- If a review doesn't follow the three-part format or runs far over length, re-run that reviewer once. If it fails again, proceed with four and note it in the report.
- Don't summarize or pre-digest the reviews for the chairman. The chairman reads them as written.
- In Round 2, reviewers also receive the crux. See `round-2.md` for the one change to the prompt.
