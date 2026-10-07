# LLM Council

A multi-agent decision-making skill inspired by Karpathy's LLM Council.

LLM Council pressure-tests a question or decision through:

1. Five independent AI advisors with different reasoning lenses.
2. Anonymous peer review of the advisors.
3. A chairman that evaluates agreement, disagreement, assumptions, evidence quality, and blind spots.
4. An optional single second reasoning round when one unresolved crux can be resolved without new external evidence.
5. A final verdict that can be a recommendation, conditional recommendation, or insufficient information.

The goal is not to manufacture certainty. The council is designed to identify when the available information is not enough to make a reliable decision.

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

## Triggering

Use explicit triggers such as:

- `council this`
- `run the council`
- `war room this`
- `pressure-test this`
- `stress-test this`
- `debate this`

It can also trigger for genuine high-stakes decisions with meaningful tradeoffs, such as choosing between options or validating a plan.

It should not trigger for ordinary factual questions, simple yes/no questions, routine creation tasks, or casual decisions with no meaningful tradeoff.

## Design principles

- Independent first-pass reasoning
- Deliberately different advisor perspectives
- Anonymous peer review
- An outsider with reduced context to surface overlooked issues
- Explicit distinction between reasoning and external evidence
- A chairman gate for `FINAL`, `ROUND_2`, or `INSUFFICIENT`
- At most one additional reasoning round
- Legitimate `INSUFFICIENT INFORMATION` outcomes
- No automatic confidence carryover between rounds
- Majority agreement is not treated as proof

## License

MIT. See [LICENSE](LICENSE).
