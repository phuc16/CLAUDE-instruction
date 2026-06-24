[![EN](https://img.shields.io/badge/lang-EN-blue?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-lightgrey?style=flat-square)](README.vi.md)

# Claude Code Guides

Practical guides for getting the most out of Claude Code in day-to-day development.

---

## Guides

### [Memory Bank Pattern](memory-bank/README.md)
Externalizes Claude's working context to `.claude/memory/` — a set of markdown files that survive context resets and multi-day sessions. Ships as a Claude Code plugin with commands `/memory-bank:start`, `/memory-bank:done`, `/memory-bank:decision`, `/memory-bank:wrapup`, and more.

### [Project-level CLAUDE.md](project-claude/README.md)
How to write a `CLAUDE.md` that actually works: what to auto-generate with `/init`, what must be added manually (business rules, gotchas, architectural decisions), and how to validate it.

---

## Quick Start

```bash
# 1. Clone this repo (once)
git clone https://github.com/phucth/CLAUDE-instruction ~/.claude-instruction

# 2. Install the Memory Bank plugin (once, globally)
# Copy plugin to cache and register — see memory-bank/README.md for details
# Then in Claude Code:
> /reload-plugins

# 3. In any project
cd your-project
claude
> /init                    # generate CLAUDE.md
> /memory-bank:init        # set up .claude/memory/
```
