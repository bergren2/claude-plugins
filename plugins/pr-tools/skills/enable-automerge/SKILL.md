---
name: enable-automerge
description: Enable auto-merge on a pull request so it merges automatically once all checks pass
argument-hint: <PR number or leave blank for current branch> [squash|merge|rebase]
---

Enable auto-merge on a GitHub pull request so it merges automatically once all required status checks pass and any required reviews are approved.

## Context gathering

1. **Identify the PR** — parse $ARGUMENTS for a PR number and/or a merge strategy keyword (`squash`, `merge`, `rebase`). If no PR number is given, detect the current branch's open PR via `gh pr view --json number,title,isDraft,state`.

2. **Check the PR state** — confirm it is open and not a draft. If it is a draft, tell the user and stop — GitHub does not allow auto-merge on draft PRs.

3. **Determine the merge strategy** — use the strategy from $ARGUMENTS if provided. Otherwise default to `squash`.

## Enabling auto-merge

Run:

```
gh pr merge --auto --<strategy> <number>
```

Where `<strategy>` is `squash`, `merge`, or `rebase`.

## Output

Report:
- The PR number and title
- The merge strategy that will be used
- Confirmation that auto-merge is now enabled
- The PR URL

## Important notes

- Auto-merge requires the repository to have branch protection rules or rulesets that enforce at least one status check or required review. If the repository has none, GitHub will merge the PR immediately rather than waiting — warn the user if this seems likely (e.g., if the PR already shows as mergeable with no pending checks).
- Do not change any other PR settings (labels, reviewers, assignees) unless the user explicitly asks.
- If auto-merge is already enabled on the PR, say so and confirm the strategy rather than re-running the command.
