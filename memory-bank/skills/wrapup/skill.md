---
description: End-of-session wrap-up — update all memory files, summarize progress, and suggest a commit message.
---

Do ALL of these steps in order:

**Step 1 — Update .claude/memory/activeContext.md:**
- Check off all items completed this session
- Update "Current File Being Worked On" to the actual current file
- Rewrite "Immediate Next Step" to be the very next concrete action
- Add any new blockers discovered this session
- Update "Last updated" timestamp

**Step 2 — Append to .claude/memory/progress.md:**
- Any decisions made this session (format: Decision → Why → Alternatives rejected)
- Any "what didn't work" discoveries

**Step 3 — Output end-of-session summary:**

📦 **Session Summary**
✅ Completed: [list items checked off]
🔨 Left in progress: [items still open]
⏭️  Next session starts at: [exact next step, copy from activeContext]
💡 Decisions logged: [N] new entries

**Step 4 — Suggest a commit message** based on all completed items this session, using Conventional Commits format:
```
git commit -m "<type>[optional scope]: <description>

[optional body — what changed and why, if non-obvious]"
```
Types: `feat` `fix` `docs` `refactor` `perf` `test` `chore` `ci`
Use `feat!` or add `BREAKING CHANGE:` in footer for breaking changes.

**Step 5 — Remind:**
"Run this if stack or patterns changed:
git add CLAUDE.md .claude/memory/techStack.md .claude/memory/systemPatterns.md"
