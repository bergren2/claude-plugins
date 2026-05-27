# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This is a Claude Code plugin marketplace repository that distributes skills installable via the Claude Code plugin system. There is no build system, test suite, or runtime code — the repository is entirely declarative JSON configs and markdown-based skill definitions.

## Architecture

The repository uses a three-level hierarchy:

1. **Marketplace** (`.claude-plugin/marketplace.json` at root) — declares the marketplace identity and lists available plugins
2. **Plugin** (`plugins/<name>/.claude-plugin/plugin.json`) — declares plugin metadata and authorship
3. **Skill** (`plugins/<name>/skills/<skill-name>/SKILL.md`) — the actual skill implementation

### Adding a New Plugin

Create a directory under `plugins/` with:
- `.claude-plugin/plugin.json` — plugin metadata
- `README.md` — usage documentation
- `skills/<skill-name>/SKILL.md` — one file per skill

Then register the plugin in the root `.claude-plugin/marketplace.json` under the `plugins` array.

Finally, add the plugin to README.md so it can easily be found.

### SKILL.md Format

Skills use YAML frontmatter followed by a markdown prompt:

```markdown
---
name: skill-name
description: One-line description shown in the UI
argument-hint: <argument description>
---

Skill prompt content here...
```

The prompt body defines the behavior Claude will follow when the skill is invoked. It should include a structured process, decision rules, and output format.

## Installation (for users)

```
/plugin marketplace add bergren2/claude-plugins
/plugin install five-whys@bergren2-claude-plugins
```
