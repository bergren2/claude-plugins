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

### `/ship-pr`

Runs a readiness gate check, writes a clean squash-merge commit, marks the PR ready (if draft), and enables auto-merge — all in one workflow. Hard stops on failing CI, change requests, or merge conflicts. Warns on pending checks or missing reviews but lets you proceed. Reads merge strategy from Claude Code memory; defaults to squash.

```
/ship-pr
/ship-pr 42
```

To save a preferred merge strategy, tell Claude: _"Remember my ship-pr merge strategy is rebase."_

### `/review-local`

Runs a local, sandboxed code review of the current branch inside the [`claude-sandbox`](https://github.com/bergren2/claude-sandbox) Dev Container, which runs Claude Code with `--dangerously-skip-permissions` behind an outbound network firewall. Drives the container headlessly via the Dev Containers CLI (no VS Code), bootstrapping the scaffolding first if it's missing, then reads back and summarizes the findings. The review runs inside the container — `--dangerously-skip-permissions` never touches the host. Requires Docker and `@devcontainers/cli`; falls back to the manual VS Code flow if those aren't available.

```
/review-local
/review-local origin/develop
```
