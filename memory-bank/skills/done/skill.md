---
description: Check off a completed task in activeContext.md and suggest a Conventional Commits message.
argument-hint: <task text>
---

In .claude/memory/activeContext.md:
- Find the line containing $ARGUMENTS under "## Exactly Where I Am"
- Change `- [ ]` to `- [x]`
- Update "Last updated" timestamp to today

Confirm: "✓ Checked off: $ARGUMENTS"

Then suggest a commit message based on what was just completed, using Conventional Commits format:
```
git commit -m "<type>[optional scope]: <description>

[optional body — what changed and why, if non-obvious]"
```
Types: `feat` `fix` `docs` `refactor` `perf` `test` `chore` `ci`
Use `feat!` or add `BREAKING CHANGE:` in footer for breaking changes.
