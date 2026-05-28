---
name: ship-pr
description: Gate-check a PR for readiness, set the merge commit, mark it ready, and enable automerge
argument-hint: <PR number or leave blank for current branch>
---

Prepare a pull request for merging by running a readiness gate, writing a clean merge commit, marking it ready for review, and enabling auto-merge — all in a single workflow.

## Step 1 — Check memory for preferences

Before doing anything else, check memory for stored preferences for this skill. Look for:

- **Merge strategy** — `squash`, `merge`, or `rebase`. Default: `squash`.
- Any other `ship-pr` preferences the user has saved.

If preferences are found, silently apply them. Do not announce them unless they affect a decision the user would otherwise be prompted about.

> **Saving preferences**: Users can store preferences by saying things like "remember to always use squash merge for ship-pr" or "remember my ship-pr merge strategy is rebase". The skill will pick them up on the next run.

## Step 2 — Identify the PR

If a PR number was given in $ARGUMENTS, use that. Otherwise detect the current branch's open PR:

```
gh pr view --json number,title,body,baseRefName,isDraft,reviewDecision,statusCheckRollup,mergeable,url
```

If no open PR exists for the current branch, stop and tell the user.

## Step 3 — Readiness gate

Display a readiness summary before taking any action. Check each dimension and mark it ✓, ✗, or ⏳:

| Dimension | How to evaluate |
|-----------|----------------|
| **Draft** | `isDraft: false` → ✓ ready; `true` → ✗ will be marked ready in Step 5 |
| **Reviews** | `reviewDecision: APPROVED` → ✓; `CHANGES_REQUESTED` → ✗ (stop); `REVIEW_REQUIRED` → ⏳ (warn, do not stop) |
| **CI checks** | All entries in `statusCheckRollup` are `SUCCESS` or `NEUTRAL` → ✓; any `FAILURE` or `ERROR` → ✗ (stop); any `PENDING` → ⏳ (warn, do not stop) |
| **Mergeable** | `mergeable: MERGEABLE` → ✓; `CONFLICTING` → ✗ (stop); `UNKNOWN` → ⏳ |

**Hard stops** (do not proceed without explicit override):
- `reviewDecision: CHANGES_REQUESTED`
- Any CI check in `FAILURE` or `ERROR`
- `mergeable: CONFLICTING`

For hard stops, report what failed and exit. Do not ask the user if they want to proceed anyway.

**Warnings** (proceed after showing the summary):
- Pending CI checks
- Reviews not yet submitted (`REVIEW_REQUIRED`)
- `mergeable: UNKNOWN`

After showing the summary, if there are only warnings (no hard stops), ask the user once whether to continue. If they confirm, proceed.

## Step 4 — Set the merge commit

Draft a commit-message-style summary of the pull request optimized for the merge history:

1. Run `git log <baseRefName>...HEAD --oneline` to see all commits.
2. Run `git diff <baseRefName>...HEAD --stat` for a file-level picture.

Produce:

- **Subject (PR title)** — imperative mood, under 72 characters. Capture the single most important thing the branch does.
- **Body (PR body)** — 3 to 5 short lines. What changed and why. No bullet headers, no section labels, no test plans. Plain prose or a tight bulleted list.

Show the proposed subject and body to the user and ask for confirmation before applying. Once confirmed:

```
gh pr edit <number> --title "<subject>" --body "<body>"
```

> **Note:** This replaces the PR title and body. If the existing body has sections a reviewer needs (test plan, screenshots, breaking changes), warn the user that those will be replaced and confirm before proceeding.

## Step 5 — Mark ready (if draft)

If the PR was a draft, mark it ready now:

```
gh pr ready <number>
```

## Step 6 — Enable auto-merge

Enable auto-merge using the merge strategy from memory (or `squash` if none stored):

```
gh pr merge --auto --<strategy> <number>
```

## Output

After all steps complete, report:

- PR number, title, and URL
- Merge strategy that will be used
- Confirmation that auto-merge is enabled
- A one-line summary of what was done (e.g., "Marked ready, set merge commit, enabled squash automerge.")
