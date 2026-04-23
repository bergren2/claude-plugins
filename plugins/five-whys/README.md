# five-whys

A Claude Code skill for facilitating [Five Whys](https://en.wikipedia.org/wiki/Five_whys) root cause analysis interactively.

## Usage

```
/five-whys <problem description>
```

Or invoke without arguments and the skill will prompt you for the problem.

The skill works through the "Why?" chain with you conversationally, stops when a root cause is found (not just at count five), handles branching causes, and concludes with concrete corrective actions.

## Installation

```
/plugin marketplace add bergren2/claude-plugins
/plugin install five-whys@bergren2-claude-plugins
```
