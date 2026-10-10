# Contributing to LLM Council

Thanks for considering a contribution. Bug reports, reproducible examples, documentation fixes, and carefully scoped improvements are useful.

## Before you start

- Check existing issues to see whether the problem or idea has already been discussed.
- For a substantial change, open an issue first so the intended behavior can be discussed before you invest time in implementation.
- Keep changes focused. Avoid bundling unrelated prompt, workflow, and documentation changes into one pull request.

## Reporting a bug

Use the bug report issue template. Include, where possible:

- The LLM tool and environment used
- The steps or prompt needed to reproduce the behavior
- The expected result and the actual result
- Relevant output with private information and credentials removed

Do not include API keys, private user data, or confidential material in issues.

## Proposing a feature

Use the feature request template. Explain the problem, the intended behavior, and relevant trade-offs. A proposed feature should fit the project's focus on adaptive reasoning effort, evidence-aware decisions, selective review, and explicit uncertainty.

## Pull requests

1. Describe the problem and the reason for the change.
2. Explain what changed and any behavior users should expect.
3. Update documentation when behavior or setup instructions change.
4. Test the affected workflow in the LLM tool you have available. If a test was not possible, state that clearly.
5. Keep performance, token-use, and quality claims tied to reproducible observations. Do not present a single run as a universal guarantee.
6. Remove secrets, private prompts, and personal information from examples and logs.

There is currently no formal automated test suite documented for this repository, so include concise manual verification steps where applicable.

## Scope and review

Contributions are reviewed for clarity, usefulness, consistency with the project's design, and honest treatment of limitations. Opening a pull request does not guarantee acceptance.

By contributing, you agree that your contribution may be distributed under the repository's MIT License.
