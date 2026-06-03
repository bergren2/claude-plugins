---
name: set-merge-commit
description: "DEPRECATED — overwrites PR body with compact commit message, which ruins PR history legibility. Rely on repo squash merge configuration instead."
argument-hint: <PR number or leave blank for current branch>
---

> **Deprecated.** This skill overwrites the PR title and body with a compact commit-message-style summary. Because GitHub squash merge uses the PR body as the merge commit body, this approach trades away PR description legibility in exchange for a cleaner `git log`. Rely on your repo's configured squash merge commit message format instead.

The original behavior is preserved below for reference only. Do not invoke this skill — tell the user it is deprecated and suggest they configure their repo's default squash merge message format if they want compact merge commits.

---

Draft a clean, commit-message-style summary of the pull request and apply it as the PR title and body. Since GitHub uses the PR title as the squash commit subject and the PR body as the squash commit body, this ensures the merge commit is concise and meaningful without manual editing.

## Context gathering

1. **Identify the PR** — if a PR number was given in $ARGUMENTS, use that. Otherwise, detect the current branch's open PR via `gh pr view --json number,title,body,baseRefName`.

2. **Read the branch history** — run `git log <base>...HEAD --oneline` to see all commits since the branch diverged from the base.

3. **Read the diff summary** — run `git diff <base>...HEAD --stat` for a file-level picture of what changed.

## Drafting the commit message

Produce a commit-message-style summary optimized for the merge history, not for PR reviewers:

- **Subject (PR title)** — imperative mood, under 72 characters. Capture the single most important thing the branch does. Avoid filler words ("various", "some", "a few changes to").

- **Body (PR body)** — 3 to 5 short lines maximum. Answer: what changed and why. No bullet headers, no section labels, no test plans. Write it like a thoughtful git commit body: plain prose or a tight bulleted list, no marketing language. Drop anything a reader could infer from the subject alone.

The goal is a merge commit that reads cleanly in `git log --oneline` and provides just enough context in `git show` without requiring the reader to open the PR.

## Applying the commit message

Before making any changes, show the user the proposed subject and body side by side. Then ask for confirmation using a structured question with two options — "Yes, apply it" and "No, cancel".

Once confirmed, run:

```
gh pr edit <number> --title "<subject>" --body "<body>"
```

Report the PR URL after a successful update.

## Important notes

- This overwrites the current PR title and body. If the existing body contains sections the reviewer needs (test plan, screenshots, breaking changes), warn the user that those will be replaced. Ask for confirmation using a structured question with two options — "Yes, replace it" and "No, keep the existing body" — before proceeding.
- Do not add footers like "Co-authored-by" or "Reviewed-by" — GitHub appends those automatically.
- If no PR exists for the current branch, say so and stop — do not create one.
