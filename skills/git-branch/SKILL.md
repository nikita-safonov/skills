---
name: git-branch
description: >-
  Use when the user asks to create a new Git branch.
  Analyzes repository conventions, determines the appropriate branch name
  and base branch, validates the branch, creates it, and verifies the result.
---

# Git Branch

## Purpose

Create a new Git branch following the repository's existing naming conventions and Git workflow.

The workflow includes:
- Inspecting the current repository state.
- Determining the source (base) branch.
- Generating or validating the new branch name.
- Checking for existing branches and naming conflicts.
- Creating the new branch.
- Verifying the result.

## Arguments

- `branch_name` (optional): Name of the new branch. If omitted, generate a descriptive name based on the user's task and repository conventions.
- `base_branch` (optional): Branch from which the new branch should be created. Defaults to the current branch.

## Workflow

### 1. Inspect Repository State

Determine the current branch:

```bash
git branch --show-current
```

Check repository status:

```bash
git status --short
```

Inspect local and remote branches:

```bash
git branch --list
git branch -r
```

Check available remotes:

```bash
git remote -v
```

**Safety checks:**

- Verify that the current directory is a Git repository.
- If a merge, rebase, cherry-pick, or revert operation is in progress, stop and inform the user.
- If there are uncommitted changes, inform the user before switching branches. Never commit, stash, discard, or overwrite changes automatically.
- If the repository has no commits, stop and inform the user.
- Do not modify Git configuration or repository settings.

### 2. Determine Branch Name

If `branch_name` was provided, use it.

Otherwise, generate a descriptive name based on the user's task.

First, inspect existing branch names and repository documentation to identify naming conventions.

If no convention exists, use these prefixes:

| Prefix | Usage |
| --- | --- |
| `feat/` | New functionality |
| `fix/` | Bug fixes |
| `refactor/` | Code restructuring |
| `chore/` | Maintenance tasks |
| `docs/` | Documentation changes |
| `test/` | Testing-related changes |

**Naming guidelines:**

- Use lowercase letters.
- Use kebab-case for multiword names.
- Keep names concise and descriptive.
- Avoid spaces and unnecessary special characters.
- Do not invent issue identifiers or ticket prefixes.
- Follow existing repository conventions whenever possible.

Examples:

```text
feat/user-profile
fix/payment-validation
refactor/auth-service
chore/update-dependencies
docs/api-reference
test/user-registration
```

If the task is unclear and a meaningful branch name cannot be generated, ask the user.

### 3. Determine Base Branch

Determine the base branch:

1. Use the provided `base_branch` argument if available.
2. Otherwise, use the current branch.

Verify that the selected base branch exists locally or as a remote-tracking branch.

Do not silently switch to `main`, `master`, or another branch.

If the requested base branch does not exist, stop and ask the user how to proceed.

### 4. Validate Branch Name

Validate the proposed branch name:

```bash
git check-ref-format --branch "<branch_name>"
```

Check whether the branch already exists locally:

```bash
git branch --list "<branch_name>"
```

Check whether the branch already exists remotely:

```bash
git branch -r --list "*/<branch_name>"
```

**Conflict handling:**

- If the branch already exists locally, do not overwrite or recreate it.
- If the branch already exists remotely, do not create a conflicting branch.
- If the requested branch is the current branch, inform the user that it is already checked out.
- If the name is invalid, suggest a corrected name and ask for confirmation.

Never delete or rename existing branches automatically.

### 5. Create Branch

Before creation, confirm that:
- The branch name is valid.
- The base branch exists.
- No conflicting branch exists.
- The working tree is safe to switch.

If there are uncommitted changes, ask the user whether to carry them into the new branch before proceeding.

Create the branch from the selected base branch:

```bash
git switch -c "<branch_name>" "<base_branch>"
```

If the base branch is the current branch, the base argument may be omitted:

```bash
git switch -c "<branch_name>"
```

Do not use force options or overwrite existing branches.

Do not push the new branch to the remote unless explicitly requested.

### 6. Verify and Report

Verify the current branch:

```bash
git branch --show-current
```

Verify repository status:

```bash
git status --short
```

After successful creation, report:
- New branch name.
- Base branch.
- Confirmation that the branch was created and checked out.
- Any remaining uncommitted changes.

If branch creation fails:
- Report the relevant Git error.
- Do not attempt unrelated fixes.
- Do not delete or modify existing branches.
- Return control to the user.

## Constraints

- Never overwrite or delete existing branches.
- Never force-create a branch.
- Never commit, stash, or discard user changes automatically.
- Never push branches unless explicitly requested.
- Never switch branches during an unfinished merge, rebase, cherry-pick, or revert.
- Never invent issue identifiers.
- Always follow existing repository naming conventions.
- Never silently change the requested base branch.
- If critical information is missing, ask the user.
- If a Git command fails, report the error and stop rather than attempting unrelated fixes.
