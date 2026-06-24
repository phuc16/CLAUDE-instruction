# System Patterns

## Document Structure
Each guide follows: problem statement → solution overview → detailed spec → examples → step-by-step workflow.

## Language Convention
All guide prose is in Vietnamese. Code blocks, file paths, CLI commands, and skill invocation syntax are in English.

## Plugin File Format
memory-bank is a Claude Code plugin at `memory-bank/` in this repo:
- Each operation is a separate skill file at `memory-bank/skills/<op>/skill.md`
- Uses `$ARGUMENTS` as the placeholder for user-supplied text
- Slash command `/using-memory-bank` from `memory-bank/.claude/commands/using-memory-bank.md`
- Operations invoked as `/memory-bank:<op>` (e.g. `/memory-bank:start`)
- Installed via `/plugin install memory-bank@phuc16-claude-instruction`

## Code Block Fidelity
Code blocks inside guides are canonical — they represent real commands or file content. Never paraphrase or summarize them; preserve verbatim.

## Naming Conventions
- Guide dirs: lowercase with README.md + README.vi.md (`memory-bank/`, `project-claude/`)
- Memory files: camelCase `.md` (`activeContext.md`, `techStack.md`)
- Plugin skills: kebab-case dirs under `memory-bank/skills/`

## gitignore Rule
`activeContext.md` and `progress.md` are personal state — never commit. `techStack.md`, `systemPatterns.md`, and `CLAUDE.md` are team knowledge — always commit.
