---
name: git-merge-request
description: >-
  Prepare a pull or merge request from a working branch by reviewing its commits and diff and writing an English title
  and description. Use when asked to draft, prepare, create, or submit a pull or merge request. Publish the branch or
  request only when the user's instructions authorize that action.
---

# Git Merge Request

## Choose the requested outcome

- For a draft title, description, or preparation-only request, perform read-only analysis and return the content.
- When asked to push the branch, publish it after reviewing the changes.
- When asked to create or submit the request, push the branch if needed and create the request through the hosting
  platform's available tools. A request to create a draft on the hosting platform also authorizes publication as a
  draft.
- Respect instructions such as "do not push" or "do not create the request". Existing authorization is sufficient; do
  not ask the user to approve the same action again.

## Resolve the branches

Accept an optional `target_branch`; otherwise determine the target from the repository's documented workflow or hosting
platform's default-branch metadata. Resolve the intended remote (`origin` when appropriate) before querying its symbolic
HEAD, which is another available source:

```bash
git symbolic-ref --quiet --short "refs/remotes/<remote>/HEAD"
```

If the available sources conflict or do not identify the target, ask the user. Do not guess `main` or `master`.

Inspect the source branch, repository status, and remotes:

```bash
git branch --show-current
git status --short
git remote -v
```

- Require a working branch distinct from the target. If HEAD is detached or the source is the repository's default
  branch, ask the user to select a working branch.
- Use `origin` when it is the intended remote; resolve a missing or ambiguous remote from the user's instructions and
  repository workflow.
- Report uncommitted changes as excluded from the request. Do not stage, commit, discard, or stash them.
- Stop on an unfinished merge, rebase, cherry-pick, or revert, or unresolved conflicts.

Fetch the selected target and verify its remote-tracking ref before comparing. Substitute the resolved remote and branch
names for these placeholders:

```bash
git fetch "<remote>" "<target_branch>"
git rev-parse --verify "refs/remotes/<remote>/<target_branch>"
```

If the target cannot be fetched or verified, report the error instead of silently comparing against another branch.

## Review and write

```bash
git log "<remote>/<target_branch>..HEAD" --oneline
git diff "<remote>/<target_branch>...HEAD" --stat
git diff "<remote>/<target_branch>...HEAD"
```

Read the actual diff as well as the commits. If there are no commits ahead of the target or the diff is empty, report
that there is no changeset to submit.

Write the title and description in English. Follow the repository's request template when present. Otherwise use the
sections below, keeping the detail proportional to the changes:

```markdown
## Summary

Describe the resulting behavior and important implementation details.

## Motivation

Explain the problem the changes address.

## Testing

List checks actually performed and their results, or state that tests were not run.
```

Do not invent requirements, motivations, issue identifiers, or test results. Preserve issue references supplied by the
user or required by the repository's workflow.

## Publish when authorized

For preparation-only requests, skip this section and return the title and description.

Before publication, verify that the source branch and reviewed commit have not changed. Push only the source branch to
the intended remote:

```bash
git push --set-upstream "<remote>" "<source_branch>"
```

Do not force-push, rewrite history, create commits, or push directly to the target. If the push fails, report the error
and stop publication; do not overwrite remote work.

When creation or submission was requested, use the hosting platform's tools to create the request with the reviewed
source, target, title, description, and requested draft status. Check for an existing request before creating a
duplicate. If creation tools are unavailable, provide the prepared content and explain what remains to be done.

Report source and target branches, the prepared content, and publication status. Include a request URL only after
confirming that the request exists. Distinguish content preparation, branch push, and request creation in the result.
