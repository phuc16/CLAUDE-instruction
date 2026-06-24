---
description: Discovers and invokes memory-bank skills. Use at session start or when you need to pick the right memory operation.
---

# Using Memory Bank

The Memory Bank externalizes project context to `.claude/memory/` so Claude survives context resets and multi-day sessions.

## Skill Map

| When | Use skill |
|------|-----------|
| Starting a session | `memory-bank:start` |
| Logging a decision | `memory-bank:decision` |
| Logging a gotcha/blocker | `memory-bank:gotcha` |
| Checking off a completed task | `memory-bank:done` |
| Ending a session | `memory-bank:wrapup` |
| Checking bank health | `memory-bank:status` |
| First-time setup | `memory-bank:init` |

## Memory Files

| File | Committed | Purpose |
|------|-----------|---------|
| `techStack.md` | yes | languages, versions, dev commands |
| `systemPatterns.md` | yes | error handling, logging, testing conventions |
| `activeContext.md` | no | current task, checklist, next step, blockers |
| `progress.md` | no | decision log |
