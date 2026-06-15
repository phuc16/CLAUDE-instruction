---
description: Memory Bank manager — read, update, and summarize project memory files
argument-hint: <start|decision:<text>|gotcha:<text>|done:<task>|wrapup|status|init>
---

You are managing the Memory Bank for this project.
The Memory Bank lives in the `memory/` folder at the project root.
Act based on $ARGUMENTS:

---

## start

Read ALL memory files in this order (skip if file doesn't exist):
1. memory/activeContext.md
2. memory/progress.md
3. memory/systemPatterns.md
4. memory/techStack.md

Then output a briefing in this exact format — nothing else before it:

📍 **Current Task:** [task từ activeContext]
✅ **Last completed:** [item cuối cùng đã check off, hoặc "None yet"]
🔨 **In progress:** [item có "IN PROGRESS" tag, hoặc "None"]
⏭️  **Next step:** [Immediate Next Step từ activeContext]
🚧 **Blockers:** [Blockers nếu có, hoặc "None"]

Then ask: "Ready to continue?"

If memory/ doesn't exist: reply "No Memory Bank found. Run `/memory-bank init` to initialize."

---

## decision:<text>

Append to memory/progress.md:

### [today's date]: [extract a short title from <text>]
**Decision:** [<text>]
**Why:** [REQUIRED — if not provided in <text>, ask before writing]
**Alternatives rejected:** [if mentioned in <text>, otherwise omit]

Then confirm: "✓ Logged to progress.md"

---

## gotcha:<text>

1. Append to memory/activeContext.md under "## Blockers / Open Questions":
   - [<text>]
2. If <text> is about stack config or versions → also append to memory/techStack.md
3. If <text> is about a coding pattern → also append to memory/systemPatterns.md
4. Confirm which file(s) were updated: "✓ Logged to [files]"

---

## done:<task>

In memory/activeContext.md:
- Find the line containing <task> under "## Exactly Where I Am"
- Change `- [ ]` to `- [x]`
- Update "Last updated" timestamp to today

Confirm: "✓ Checked off: <task>"

---

## wrapup

Do ALL of these steps in order:

**Step 1 — Update memory/activeContext.md:**
- Check off all items completed this session
- Update "Current File Being Worked On" to the actual current file
- Rewrite "Immediate Next Step" to be the very next concrete action
- Add any new blockers discovered this session
- Update "Last updated" timestamp

**Step 2 — Append to memory/progress.md:**
- Any decisions made this session (format: Decision → Why → Alternatives rejected)
- Any "what didn't work" discoveries

**Step 3 — Output end-of-session summary:**

📦 **Session Summary**
✅ Completed: [list items checked off]
🔨 Left in progress: [items still open]
⏭️  Next session starts at: [exact next step, copy from activeContext]
💡 Decisions logged: [N] new entries

**Step 4 — Remind:**
"Run this if stack or patterns changed:
git add CLAUDE.md memory/techStack.md memory/systemPatterns.md"

---

## status

1. List all files in memory/ with last-modified dates
2. Flag any file not updated in > 7 days as "⚠️ possibly stale"
3. Run /memory to show what Claude currently has loaded

---

## init

Create the Memory Bank from scratch:

1. Create memory/ folder if not exists
2. Create 4 files: memory/activeContext.md, memory/progress.md,
   memory/techStack.md, memory/systemPatterns.md
3. Append to .gitignore (don't overwrite):
   memory/activeContext.md
   memory/progress.md
4. Read the codebase and fill in memory/techStack.md:
   - Detect languages, frameworks, libraries with versions
   - Note non-obvious config (connection limits, key prefixes, deprecated items)
   - Add local dev commands (how to run, test, connect to DB/cache)
   - Add required environment variables
5. Read the codebase and fill in memory/systemPatterns.md:
   - Detect error handling pattern
   - Detect repository/service layer pattern
   - Detect logging convention
   - Detect testing convention
6. Write skeleton into memory/activeContext.md:
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
7. Write skeleton into memory/progress.md:
   # Progress & Decisions
   ## Decisions Made
   [none yet]
   ## What Didn't Work
   [none yet]
8. Confirm: "✓ Memory Bank initialized.
   Review memory/techStack.md and memory/systemPatterns.md —
   add business rules and gotchas that I couldn't detect from code."