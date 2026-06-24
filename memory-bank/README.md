[![EN](https://img.shields.io/badge/lang-EN-blue?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-lightgrey?style=flat-square)](README.vi.md)

# Memory Bank — Claude Code Plugin

Externalizes Claude's working context to `.claude/memory/` so it survives context resets and multi-day sessions.

## Installation

```bash
# From this repo root — copy plugin to cache
mkdir -p ~/.claude/plugins/cache/local/memory-bank/1.0.0
cp -r memory-bank/. ~/.claude/plugins/cache/local/memory-bank/1.0.0/

# Register in installed_plugins.json (add this entry):
# "memory-bank@local": [{
#   "scope": "user",
#   "installPath": "/Users/<you>/.claude/plugins/cache/local/memory-bank/1.0.0",
#   "version": "1.0.0",
#   "installedAt": "<date>",
#   "lastUpdated": "<date>",
#   "gitCommitSha": null
# }]

# Then in Claude Code:
> /reload-plugins
```

## Commands

| Command | Description |
|---------|-------------|
| `/using-memory-bank` | Show skill map |
| `/memory-bank:start` | Read all memory files, output session briefing |
| `/memory-bank:decision <text>` | Log a decision to `progress.md` |
| `/memory-bank:gotcha <text>` | Log a gotcha/blocker to relevant files |
| `/memory-bank:done <task>` | Check off a task, suggest commit message |
| `/memory-bank:wrapup` | End-of-session update + summary + commit suggestion |
| `/memory-bank:status` | Check memory bank health |
| `/memory-bank:init` | Initialize from scratch |

## Memory Files

| File | Committed | Purpose |
|------|-----------|---------|
| `techStack.md` | yes | languages, versions, dev commands |
| `systemPatterns.md` | yes | error handling, logging, testing conventions |
| `activeContext.md` | no | current task, checklist, next step, blockers |
| `progress.md` | no | decision log |

## Updating

Edit skill files in `memory-bank/skills/<op>/skill.md`, then re-copy:

```bash
cp -r memory-bank/. ~/.claude/plugins/cache/local/memory-bank/1.0.0/
# Then /reload-plugins in Claude Code
```
