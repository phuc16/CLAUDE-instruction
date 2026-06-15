# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This repository stores Vietnamese-language guides for Claude Code best practices:

- `memory-bank.MD` — Full specification and workflow for the Memory Bank pattern: externalizing project context to `memory/*.md` files so Claude survives context resets and multi-day work sessions. Includes the complete `~/.claude/skills/memory-bank/skill.md` content to copy-paste.
- `project-claude.MD` — Guide for authoring project-level `CLAUDE.md` files: what to auto-generate via `/init`, what must be added manually (business rules, gotchas, architectural decisions), and how to validate quality.

## Memory Bank Pattern (summary)

The Memory Bank lives in `memory/` at the project root — four files:

| File | Committed | Purpose |
|---|---|---|
| `techStack.md` | yes | languages, versions, non-obvious config, dev commands |
| `systemPatterns.md` | yes | error handling, repo pattern, logging, testing conventions |
| `activeContext.md` | no | current task, in-progress checklist, immediate next step, blockers |
| `progress.md` | no | decision log (decision → why → alternatives rejected) |

Invoke via `/memory-bank <command>` where commands are `start`, `decision:`, `gotcha:`, `done:`, `wrapup`, `status`, `init`.

## Editing these guides

When updating the guides, preserve Vietnamese text as-is — do not translate. The skill file content inside code blocks in `memory-bank.MD` must remain exactly copy-pasteable.
