---
description: Memory Bank manager — read, update, and summarize project memory files
argument-hint: <start|decision:<text>|gotcha:<text>|done:<task>|wrapup|status|init>
---

You are managing the Memory Bank for this project.
The Memory Bank lives in the `.claude/memory/` folder at the project root.
Act based on $ARGUMENTS:

---

## start

Read ALL memory files in this order (skip if file doesn't exist):
1. .claude/memory/activeContext.md
2. .claude/memory/progress.md
3. .claude/memory/systemPatterns.md
4. .claude/memory/techStack.md

Then output a briefing in this exact format — nothing else before it:

📍 **Current Task:** [task từ activeContext]
✅ **Last completed:** [item cuối cùng đã check off, hoặc "None yet"]
🔨 **In progress:** [item có "IN PROGRESS" tag, hoặc "None"]
⏭️  **Next step:** [Immediate Next Step từ activeContext]
🚧 **Blockers:** [Blockers nếu có, hoặc "None"]

Then ask: "Ready to continue?"

**IMPORTANT — after the briefing, if the user describes a new task or pastes an error/issue:**
DO NOT start fixing or investigating yet.
First, write the task into .claude/memory/activeContext.md:
- Set "## Current Task" to a 1-line summary of what the user described
- Under "## Exactly Where I Am", add checklist items for the subtasks you can infer
- Set "## Immediate Next Step" to the first concrete action
- Update "Last updated" timestamp
Then confirm: "✓ Task logged. Ready to start — just say go."
Only begin actual work after the user confirms.

If .claude/memory/ doesn't exist: reply "No Memory Bank found. Run `/memory-bank init` to initialize."

---

## decision:<text>

Append to .claude/memory/progress.md:

### [today's date]: [extract a short title from <text>]
**Decision:** [<text>]
**Why:** [REQUIRED — if not provided in <text>, ask before writing]
**Alternatives rejected:** [if mentioned in <text>, otherwise omit]

Then confirm: "✓ Logged to progress.md"

---

## gotcha:<text>

1. Append to .claude/memory/activeContext.md under "## Blockers / Open Questions":
   - [<text>]
2. If <text> is about stack config or versions → also append to .claude/memory/techStack.md
3. If <text> is about a coding pattern → also append to .claude/memory/systemPatterns.md
4. Confirm which file(s) were updated: "✓ Logged to [files]"

---

## done:<task>

In .claude/memory/activeContext.md:
- Find the line containing <task> under "## Exactly Where I Am"
- Change `- [ ]` to `- [x]`
- Update "Last updated" timestamp to today

Confirm: "✓ Checked off: <task>"

Then suggest a commit message based on what was just completed, using Conventional Commits format:
```
git commit -m "<type>[optional scope]: <description>

[optional body — what changed and why, if non-obvious]"
```
Types: `feat` `fix` `docs` `refactor` `perf` `test` `chore` `ci`
Use `feat!` or add `BREAKING CHANGE:` in footer for breaking changes.

---

## wrapup

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

---

## status

1. List all files in .claude/memory/ with last-modified dates
2. Flag any file not updated in > 7 days as "⚠️ possibly stale"
3. Run /memory to show what Claude currently has loaded

---

## init

Create the Memory Bank from scratch:

1. Create .claude/memory/ folder if not exists
2. Create 4 files: .claude/memory/activeContext.md, .claude/memory/progress.md,
   .claude/memory/techStack.md, .claude/memory/systemPatterns.md
3. Read the codebase and fill in .claude/memory/techStack.md:
   - Detect languages, frameworks, libraries with versions
   - Note non-obvious config (connection limits, key prefixes, deprecated items)
   - Add local dev commands (how to run, test, connect to DB/cache)
   - Add required environment variables
4. Read the codebase and fill in .claude/memory/systemPatterns.md:
   - Detect error handling pattern
   - Detect repository/service layer pattern
   - Detect logging convention
   - Detect testing convention
5. Write skeleton into .claude/memory/activeContext.md:
   # Active Context
   Last updated: [today]
   ## Current Task
   [not set]
   ## Exactly Where I Am
   [no tasks yet]
   ## Current File Being Worked On
   [none]
   ## Immediate Next Step
   [not set]
   ## Blockers / Open Questions
   [none]
6. Write skeleton into .claude/memory/progress.md:
   # Progress & Decisions
   ## Decisions Made
   [none yet]
   ## What Didn't Work
   [none yet]
7. Output exactly:

   ✓ Memory Bank initialized.

   Add personal state files to .gitignore?
   (activeContext.md and progress.md — these are yours alone, not for teammates)

   Y (recommended) — append to .gitignore (prevents merge conflicts when teammates each update their own "current task")
                     These files track your personal working state — not meant to be shared.
   n — skip (fine for a personal/solo repo, or if you prefer to manage .gitignore yourself)

   ---
   Review these two files once done — they're what I detected from code:

   - .claude/memory/techStack.md — [one-line summary of what was detected]
   - .claude/memory/systemPatterns.md — [one-line summary of what was detected]

   Add any business rules or gotchas I couldn't detect from code.

   Then wait for Y/n and append to .gitignore if Y.
