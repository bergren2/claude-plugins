# pr-tools

A suite of skills for managing GitHub pull requests via the `gh` CLI.

## Installation

```
/plugin marketplace add bergren2/claude-plugins
/plugin install pr-tools@bergren2-claude-plugins
```

## Skills

### `/update-pr`

Refreshes a stale pull request title and description to accurately reflect the current state of the branch. After a PR accumulates additional commits, this skill reads the branch history and diff and rewrites the PR metadata — while preserving intentional structure like test plans, screenshots, and breaking change notes.

```
/update-pr
/update-pr 42
```

### `/enable-automerge`

Enables auto-merge on a pull request so it merges automatically once all required status checks pass and reviews are approved. Defaults to squash merge; pass a strategy to override.

```
/enable-automerge
/enable-automerge 42
/enable-automerge 42 rebase
```

### `/set-merge-commit`

Drafts a concise, commit-message-style summary of the pull request and applies it as the PR title and body. Since GitHub uses these for the squash commit, this keeps the merge history clean without manual editing.

```
/set-merge-commit
/set-merge-commit 42
```
