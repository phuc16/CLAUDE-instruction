# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This repository stores Vietnamese-language guides for Claude Code best practices:

- `memory-bank/` — Claude Code plugin for the Memory Bank pattern: externalizes project context to `.claude/memory/` files so Claude survives context resets and multi-day work sessions. Install via `/plugin install memory-bank@local`.
- `project-claude/` — Guide for authoring project-level `CLAUDE.md` files: what to auto-generate via `/init`, what must be added manually (business rules, gotchas, architectural decisions), and how to validate quality.

## Memory Bank Pattern (summary)

The Memory Bank lives in `.claude/memory/` at the project root — four files:

| File | Committed | Purpose |
|---|---|---|
| `techStack.md` | yes | languages, versions, non-obvious config, dev commands |
| `systemPatterns.md` | yes | error handling, repo pattern, logging, testing conventions |
| `activeContext.md` | no | current task, in-progress checklist, immediate next step, blockers |
| `progress.md` | no | decision log (decision → why → alternatives rejected) |

Invoke via `/memory-bank:<command>` — commands are `start`, `decision`, `gotcha`, `done`, `wrapup`, `status`, `init`. Use `/using-memory-bank` for the skill map.

## Editing these guides

When updating the guides, preserve Vietnamese text as-is — do not translate. The plugin source lives in `memory-bank/` — edit skill files there, then re-copy to the plugin cache.
