# Tech Stack

## Format
- Markdown (`.MD` extension, uppercase — matches existing files)
- Vietnamese language throughout all guide content
- GitHub-flavored Markdown with fenced code blocks

## Files
| File | Purpose |
|---|---|
| `memory-bank.MD` | Full Memory Bank pattern spec + skill.md content to copy-paste |
| `project-claude.MD` | Guide for authoring project-level CLAUDE.md files |
| `CLAUDE.md` | Claude Code context for this repo itself |

## No Build System
Pure documentation — no package manager, no build step, no test runner.

## Git
- Branch: `main`
- `.gitignore`: excludes `.claude/memory/activeContext.md` and `.claude/memory/progress.md`
- Commit sharable docs (`techStack.md`, `systemPatterns.md`, `CLAUDE.md`), not personal state files
