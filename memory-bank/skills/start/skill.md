---
description: Read all memory files and output a session briefing. Use at the start of every session.
---

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

If .claude/memory/ doesn't exist: reply "No Memory Bank found. Run `/memory-bank:init` to initialize."
