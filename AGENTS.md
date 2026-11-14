# Repository Guidelines

## Markdown tables

Use compact GitHub-flavored Markdown tables in all Markdown files, including skill guides and repository documentation.
Follow the current style in `COMMON.md` and `BACKEND.md`:

```markdown
| Skill | Description |
| --- | --- |
| `example` | A short description. |
```

- Use one space between each cell's content and its surrounding pipes.
- Use `---` in separator cells; preserve alignment markers such as `:---` when needed.
- Do not pad cells or extend separator dashes to visually align columns in the source.
- Keep each table row on one source line, even when it exceeds the prose line-length limit.
- Preserve cell content and escape literal pipes as `\|`.
- Do not let formatters expand compact tables. If a formatting tool pads columns, restore the compact style before
  finishing the edit.

## Markdown workflow

Use Node.js 22 or newer and the pinned `markdownlint-cli2` version below. Run commands from the repository root;
`.markdownlint-cli2.jsonc` selects the Markdown files and enforces compact table spacing.

Check Markdown before finishing changes:

```bash
npx --yes markdownlint-cli2@0.24.1
```

The GitHub Actions workflow `.github/workflows/markdown.yml` runs this same check on every pull request. It reports
formatting errors without changing files.

Apply fixable formatting corrections, then rerun the check:

```bash
npx --yes markdownlint-cli2@0.24.1 --fix
npx --yes markdownlint-cli2@0.24.1
```

For a focused edit, limit auto-fixes to the edited files by using `--no-globs` and explicit paths. Review the diff
after auto-fixes; remaining findings may need manual edits. Keep table separator cells as `---` with any required
alignment markers. Do not run Prettier on Markdown: its table padding conflicts with this repository's style.
