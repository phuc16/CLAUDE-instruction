# System Patterns

## Document Structure
Each guide follows: problem statement → solution overview → detailed spec → examples → step-by-step workflow.

## Language Convention
All guide prose is in Vietnamese. Code blocks, file paths, CLI commands, and skill invocation syntax are in English.

## Skill File Format
Skill content embedded in `memory-bank.MD` must remain exactly copy-pasteable:
- Wrapped in a fenced code block (` ```markdown ... ``` `)
- Uses `$ARGUMENTS` as the placeholder for the argument passed to the skill
- Section headers (`## command-name`) are the dispatch keys

## Code Block Fidelity
Code blocks inside guides are canonical — they represent real commands or file content. Never paraphrase or summarize them; preserve verbatim.

## Naming Conventions
- Guide files: uppercase `.MD` extension (`memory-bank.MD`, not `memory-bank.md`)
- Memory files: camelCase `.md` (`activeContext.md`, `techStack.md`)
- Skill directory: kebab-case (`~/.claude/skills/memory-bank/skill.md`)

## gitignore Rule
`activeContext.md` and `progress.md` are personal state — never commit. `techStack.md`, `systemPatterns.md`, and `CLAUDE.md` are team knowledge — always commit.
