---
description: Initialize the Memory Bank from scratch — create all four memory files and populate them from the codebase.
---

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

   Y (recommended) — append to .gitignore
   n — skip

   ---
   Review these two files once done — they're what I detected from code:

   - .claude/memory/techStack.md — [one-line summary of what was detected]
   - .claude/memory/systemPatterns.md — [one-line summary of what was detected]

   Add any business rules or gotchas I couldn't detect from code.

   Then wait for Y/n and append to .gitignore if Y.
