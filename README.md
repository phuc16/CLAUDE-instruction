[![EN](https://img.shields.io/badge/lang-EN-blue?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-lightgrey?style=flat-square)](README.vi.md)

# Claude Code Guides

Practical guides for getting the most out of Claude Code in day-to-day development.

---

## Guides

### [Memory Bank Pattern](memory-bank/README.md)
Externalizes Claude's working context to `.claude/memory/` — a set of markdown files that survive context resets and multi-day sessions. Includes a ready-to-install skill (`/memory-bank`) with commands for `start`, `done`, `decision`, `wrapup`, and more.

### [Project-level CLAUDE.md](project-claude/README.md)
How to write a `CLAUDE.md` that actually works: what to auto-generate with `/init`, what must be added manually (business rules, gotchas, architectural decisions), and how to validate it.

---

## Quick Start

```bash
# 1. Install the Memory Bank skill (once, globally)
mkdir -p ~/.claude/skills/memory-bank
cp memory-bank/skill.MD ~/.claude/skills/memory-bank/skill.md

# 2. In any project
cd your-project
claude
> /init                  # generate CLAUDE.md
> /memory-bank init      # set up .claude/memory/
```
