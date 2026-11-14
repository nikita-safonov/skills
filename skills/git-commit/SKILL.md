---
name: git-commit
description: >-
  Use when the user asks to commit changes in a Git repository.
  Inspects staged, unstaged, and untracked changes, analyzes the diff,
  generates an accurate Conventional Commit message in English,
  stages the intended changes, and creates the commit.
---

# Git Commit

## Purpose

Analyze the current Git repository, prepare a meaningful commit message, and create a Git commit containing the intended changes.

The workflow includes:
- Inspecting repository status and pending changes.
- Reviewing staged, unstaged, and untracked files.
- Understanding what changed and why.
- Generating a Conventional Commit message.
- Staging the intended changes.
- Creating and verifying the commit.

## Workflow

### 1. Inspect Repository State

Check the current branch:

```bash
git branch --show-current
```

Check the repository status:

```bash
git status --short
```

Inspect all changes:

```bash
git diff HEAD
git diff --stat HEAD
git diff --cached
```

Review untracked files separately. Inspect their contents when necessary.

**Safety checks:**

- If the repository has no changes, inform the user and stop.
- Identify potentially sensitive files, including `.env`, credentials, private keys, and tokens. Never stage them automatically.
- Do not include ignored files, generated artifacts, dependencies, or unrelated changes.
- If existing staged changes appear unrelated to the requested work, ask the user before proceeding.
- If the intended commit scope is unclear, ask the user for clarification.

### 2. Analyze Changes

Understand the complete changeset before generating the commit message.

Identify:
- The primary purpose of the changes.
- New functionality.
- Bug fixes.
- Refactoring and architectural improvements.
- Documentation changes.
- Tests added or modified.
- Configuration and dependency changes.

Inspect actual code changes rather than relying exclusively on filenames or previous commit messages.

### 3. Generate Commit Message

Use the Conventional Commits specification.

**Required format:**

```text
<type>(<scope>): <description>
```

The scope is optional.

Supported types:

| Type | Usage |
| --- | --- |
| `feat` | New functionality |
| `fix` | Bug fixes |
| `docs` | Documentation changes |
| `style` | Formatting and code style changes |
| `refactor` | Code restructuring without changing behavior |
| `perf` | Performance improvements |
| `test` | Adding or updating tests |
| `build` | Build system and dependency changes |
| `ci` | CI/CD configuration changes |
| `chore` | Maintenance and miscellaneous tasks |
| `revert` | Reverting previous changes |

**Message guidelines:**

- Always write commit messages in English, regardless of the conversation language.
- Use imperative wording.
- Start the description with a lowercase letter.
- Keep the summary concise and specific.
- Describe the actual changes, not vague intentions.
- Avoid unnecessary punctuation at the end.
- Do not invent motivations or implementation details.

**Examples:**

```text
feat(auth): add password reset functionality
fix(api): handle missing user permissions
refactor(components): simplify modal state management
docs(readme): update installation instructions
test(auth): add token validation tests
```

For larger changes, add a commit body explaining the motivation and important implementation details.

### 4. Stage and Commit

After reviewing the changes and confirming that their scope is appropriate:

Stage the intended files:

```bash
git add -A
```

Before committing, verify the staged changes:

```bash
git diff --cached --stat
git diff --cached
```

Ensure that no unintended files or sensitive information are included.

Create the commit:

```bash
git commit -m "<commit-message>"
```

For a commit with a body:

```bash
git commit -m "<commit-title>" -m "<commit-body>"
```

Do not amend existing commits or rewrite Git history unless explicitly requested.

### 5. Verify and Report

After a successful commit, verify the result:

```bash
git log -1 --format="%h %s"
git status --short
```

Report to the user:
- Commit hash.
- Complete commit message.
- Summary of committed changes.
- Remaining uncommitted changes, if any.

If the commit fails:
- Show the relevant error.
- Do not attempt unrelated fixes.
- Do not bypass Git hooks or commit validation.
- Return control to the user.

## Constraints

- Commit messages must always be written in English.
- Always follow Conventional Commits.
- Never invent changes or commit descriptions.
- Never stage sensitive files or credentials.
- Never include unrelated changes without confirmation.
- Never discard or overwrite user changes.
- Never amend or rewrite existing commits unless explicitly requested.
- Never bypass Git hooks.
- Do not automatically fix linting, formatting, or test failures.
- If critical information is missing, ask the user.
- If a command fails, report the error and stop rather than attempting unrelated fixes.
