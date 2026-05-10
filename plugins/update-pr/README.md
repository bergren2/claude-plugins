# update-pr

Refreshes a stale pull request title and description to accurately reflect the current state of the branch.

## Use case

After a PR is opened, additional commits often accumulate without the title or description being updated. This skill reads the branch history and diff, then rewrites the PR metadata to match what the branch actually contains — while preserving any intentional structure (test plans, screenshots, breaking change notes) the author wrote.

## Installation

```
/plugin marketplace add bergren2/claude-plugins
/plugin install update-pr@bergren2-claude-plugins
```

## Usage

```
/update-pr
```

Run from any branch with an open PR. The skill will detect the PR automatically, show you a proposed updated title and body, and apply the changes after your confirmation.

You can also target a specific PR by number:

```
/update-pr 42
```
