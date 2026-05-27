# claude-plugins

A collection of Claude Code skills.

## Installation

Add this as a marketplace source in Claude Code:

```
/plugin marketplace add bergren2/claude-plugins
```

Then install individual plugins:

```
/plugin install five-whys@bergren2-claude-plugins
```

## Plugins

| Plugin | Description |
|--------|-------------|
| [five-whys](plugins/five-whys/) | Facilitate a Five Whys root cause analysis interactively |
| [update-pr](plugins/update-pr/) | Refresh a stale PR title and description to reflect the latest branch changes |
