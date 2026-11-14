---
name: pylines
description: >-
  Apply pylines conventions when writing, reviewing, or refactoring Python backend code and configuring Ruff or ty.
---

# Guidelines (pylines)

## Scope and precedence

Use these guides for Python backend work. Follow the user's instructions and the target repository's documented
conventions first. Apply pylines where those leave a choice; do not replace an established framework or rewrite
unrelated code just to match the recommended stack.

The bundled [pyproject.toml](pyproject.toml) is the reference for Ruff and ty settings. When adopting this
configuration, use its actual selections and overrides rather than enabling all lint rules or introducing a different
type checker. Distinguish tool-enforced checks from additional coding conventions described in the guides.

## Read the relevant guide

Load only the guides needed for the current task. Read the applicable rule together with its examples and exceptions.
For the longer SOLID guide, use its table of contents to select a principle. If the applicable rule is unclear, expand
the reading before making a recommendation.

| Topic                                                         | Guide                            |
| ------------------------------------------------------------- | -------------------------------- |
| Code style, typing, naming, immutability, exceptions, imports | [code-style.md](code-style.md)   |
| REST API, URL conventions, versioning                         | [rest.md](rest.md)               |
| Testing: AAA, parametrization, Faker/Hypothesis, pytest-xdist | [tests.md](tests.md)             |
| SOLID principles with examples                                | [solid.md](solid.md)             |
| Recommended libraries and tools                               | [our-stack.md](our-stack.md)     |
| Ruff and ty configuration                                     | [pyproject.toml](pyproject.toml) |

Keep detailed rules in their guides rather than relying on a separate summary that can omit exceptions. In reviews, cite
the relevant guide and distinguish a pylines convention from a requirement already adopted by the repository.
