---
name: update-pr
description: Refresh a stale PR title and description to reflect the latest branch changes
argument-hint: <PR number or leave blank for current branch>
---

Refresh the title and description of an existing GitHub pull request so they accurately reflect the current state of the branch.

## Context gathering

1. **Identify the PR** — if a PR number was given in $ARGUMENTS, use that. Otherwise, detect the current branch's open PR via `gh pr view --json number,title,body,baseRefName`.

2. **Check memory** — look for any saved preferences about PR formatting, description structure, or project conventions that should shape the output.

3. **Read the current PR** — capture the existing title, body, and base branch name.

4. **Read the branch history** — run `git log <base>...HEAD --oneline` to see all commits since the branch diverged from the base.

5. **Read the diff summary** — run `git diff <base>...HEAD --stat` for a file-level picture of what changed.

## Drafting the update

Using the commits, diff stat, and current PR body as inputs, produce:

- **Title** — concise (under 70 characters), present-tense imperative ("Add X", "Fix Y", "Refactor Z"). Reflect the overall intent of the branch, not just the latest commit.

- **Body** — preserve any deliberate structure from the existing body (sections like "Test plan", "Screenshots", "Breaking changes", etc.) but rewrite stale content. At minimum include:
  - A short summary paragraph (what changed and why)
  - A bulleted list of notable changes derived from commits and diff stat
  - Any existing sections from the original body, updated to match current reality

Do not invent information. If a section in the original body requires knowledge you don't have (e.g., test results, screenshots), leave a placeholder comment so the author knows to fill it in.

## Applying the update

Before making any changes, show the user the proposed new title and body and ask for confirmation.

Once confirmed, run:

```
gh pr edit <number> --title "<new title>" --body "<new body>"
```

Report the PR URL after a successful update.

## Important notes

- Never remove sections the author intentionally wrote unless they are clearly wrong or entirely about changes that no longer exist on the branch.
- If the diff is very large (hundreds of files), summarize by directory or theme rather than listing every file.
- If no PR exists for the current branch, say so and stop — do not create one.
