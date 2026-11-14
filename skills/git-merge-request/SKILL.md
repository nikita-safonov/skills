---
name: git-merge-request
description: >-
  Prepare a merge request from the current Git branch. Use when asked to create,
  prepare, or submit a merge request. Analyzes commits and code changes,
  generates a clear title and description, pushes the source branch, and
  provides the prepared merge request details.
---

# Git Merge Request

## Purpose

Prepare a high-quality merge request based on the current repository state, commit history, and code changes.

The workflow includes:
- Validating the current Git branch.
- Analyzing commits and code changes.
- Generating a meaningful merge request title and description.
- Pushing the source branch to the remote repository.
- Providing the prepared merge request details to the user.

## Arguments

- `target_branch` (optional): Target branch for the merge request. Defaults to `master`.

## Workflow

### 1. Inspect the Repository

Determine the source branch:

```bash
git branch --show-current
```

**Safety checks:**

- If the current branch is `master`, stop immediately and ask the user to switch to a working branch.
- If the source branch matches the target branch, stop.
- If the repository has uncommitted changes, inform the user. Do not stage, commit, or discard changes automatically.
- Verify that the `origin` remote exists.

Determine the target branch:
- Use the provided `target_branch` argument if available.
- Otherwise, default to `master`.

Fetch the latest target branch:

```bash
git fetch origin <target_branch>
```

Verify that the target branch exists.

### 2. Analyze Changes

Retrieve commits between the target and source branches:

```bash
git log origin/<target_branch>..HEAD --oneline
```

Inspect the complete changeset:

```bash
git diff origin/<target_branch>...HEAD --stat
git diff origin/<target_branch>...HEAD
```

Analyze the changes and identify:
- Main purpose of the changes.
- New functionality.
- Bug fixes.
- Refactoring and architectural changes.
- Breaking changes.
- Relevant implementation details.

Do not rely exclusively on commit messages. Inspect the actual code changes.

If there are no commits ahead of the target branch, stop and inform the user.

### 3. Generate Merge Request Content

Generate a concise, informative title describing the primary change.

Use imperative wording where appropriate. Do not include issue identifiers or ticket prefixes.

The description must be written in English and formatted as Markdown.

Use the following structure:

```markdown
## Summary

- Describe the main changes.
- Highlight important implementation details.

## Motivation

Explain why these changes are needed and what problem they solve.

## Testing

- Describe tests that were actually executed.
- Include relevant manual verification steps.
- Explicitly mention if tests were not run.
```

**Writing guidelines:**

- Focus on meaningful changes rather than listing every modified file.
- Be specific and technically accurate.
- Avoid unnecessary implementation details.
- Do not invent requirements, motivations, or test results.
- Keep the description proportional to the scope of changes.

### 4. Push the Source Branch

Push the current branch to the remote repository:

```bash
git push --set-upstream origin <source_branch>
```

If the push fails:
- Inspect the error.
- Do not force-push.
- Do not rewrite commit history.
- Report the problem and let the user decide how to proceed.

Do not create commits or modify the existing commit history.

### 5. Report Results

Provide the user with:

1. Merge request title.
2. Source and target branches.
3. Complete Markdown description.
4. Push status.
5. Any relevant warnings or issues.

The merge request itself must be created through the repository hosting platform. Do not claim that it was created if only the branch was pushed.

## Constraints

- Never use `master` as the source branch.
- Never push directly to the target branch.
- Never force-push.
- Never create commits automatically.
- Never stage, discard, or modify unrelated user changes.
- Never invent information about the changes or test results.
- Do not attempt to resolve unrelated repository issues.
- If critical information is missing, ask the user before proceeding.
- Stop when a safety check fails and return control to the user.
