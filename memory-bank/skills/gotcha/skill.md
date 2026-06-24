---
description: Log a gotcha, blocker, or non-obvious discovery to the appropriate memory files.
argument-hint: <gotcha text>
---

1. Append to .claude/memory/activeContext.md under "## Blockers / Open Questions":
   - [$ARGUMENTS]
2. If $ARGUMENTS is about stack config or versions → also append to .claude/memory/techStack.md
3. If $ARGUMENTS is about a coding pattern → also append to .claude/memory/systemPatterns.md
4. Confirm which file(s) were updated: "✓ Logged to [files]"
