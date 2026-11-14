---
name: pylines
description: >
  Development guidelines from the pylines project (community-of-python/pylines) — Python backend. Use whenever writing, reviewing, or refactoring code in
  backend work with FastAPI, Litestar, SQLAlchemy.
  Trigger on any code generation, code review, architecture discussion, REST API design, test
  writing, or when the user mentions "pylines", "code style", "наши гайдлайны", "guidelines", or
  asks to follow team conventions. Also trigger when writing pyproject.toml configs for ruff,
  designing class hierarchies, discussing SOLID, or structuring a project.
---

# Guidelines (pylines) — live from GitHub

## Step 1 — read the relevant guide

Pick the file from the table below, then:

- **Every guide except `solid.md`: read it whole.** They run 3–245 lines; a partial read cuts a
  rule away from the ❌/✅ example that defines it, which is worse than reading the extra lines.
- **`solid.md` (703 lines): read `index.md` first.** `sync.sh` regenerates it on every run — it
  lists each guide's headings with line numbers, so you can read just the principle you need
  (SRP, OCP, LSP, ISP, DIP are ~100 lines each) instead of the whole file.

If you cannot find a rule, say so and read the guide whole. Never answer from memory about what
these guides say — that is exactly what the cache exists to prevent.

| Тема | Файл |
|------|------|
| Python: стиль, типизация, именование, иммутабельность, исключения, импорты | `code-style.md` |
| Python: REST API, URL conventions, версионирование | `rest.md` |
| Python: тесты — AAA, parametrize, faker/hypothesis, pytest-xdist | `tests.md` |
| Python: SOLID с примерами | `solid.md` |
| Python: рекомендуемые библиотеки и инструменты | `our-stack.md` |
| Python: архитектура | `architecture-guide.md` |
| Python: эталонный ruff конфиг | `pyproject.toml` |

## Minimal always-on rules (details in the cached guides)

Python:

- Tooling: **ruff**, **uv**, line length **120**, server **granian**.
- Typing: 100% annotations, `typing.Final` on vars, `@typing.final` on classes, narrow types (Literal/TypedDict).
- Naming: verbs for functions, no `get` prefix (use fetch/build/parse…), names ≥ 8 chars, semantic.
- Classes: composition over inheritance; `@dataclass(kw_only=True, slots=True, frozen=True)`; `typing.Protocol` for interfaces.
- Exceptions: catch concrete types, LBYL over EAFP, narrow try-blocks, no custom exception classes — raise framework/stdlib ones.
- Resilience: retry anything leaving RAM (SQL/HTTP/files) via **stamina**.

Applies to both: read the matching cached guide before quoting specifics — these lists are pointers, not the standard.