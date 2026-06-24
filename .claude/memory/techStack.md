# Tech Stack

## Format
- Markdown (`.md` extension lowercase for new files)
- Vietnamese language throughout all guide content
- GitHub-flavored Markdown with fenced code blocks

## Files
| File/Dir | Purpose |
|---|---|
| `memory-bank/` | Claude Code plugin — Memory Bank pattern (skills, commands, plugin.json) |
| `project-claude/` | Guide for authoring project-level CLAUDE.md files |
| `spec-generator/` | skill.MD for spec generation |
| `CLAUDE.md` | Claude Code context for this repo itself |
| `.claude-plugin/marketplace.json` | Marketplace manifest for plugin system discovery |

## No Build System
Pure documentation — no package manager, no build step, no test runner.

## Git
- Branch: `main`
- `.gitignore`: excludes `.claude/memory/activeContext.md` and `.claude/memory/progress.md`
- Commit sharable docs (`techStack.md`, `systemPatterns.md`, `CLAUDE.md`), not personal state files
