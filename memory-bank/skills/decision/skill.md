---
description: Log a decision to progress.md. Use whenever a non-obvious technical or product decision is made.
argument-hint: <decision text>
---

Append to .claude/memory/progress.md:

### [today's date]: [extract a short title from $ARGUMENTS]
**Decision:** [$ARGUMENTS]
**Why:** [REQUIRED — if not provided in $ARGUMENTS, ask before writing]
**Alternatives rejected:** [if mentioned in $ARGUMENTS, otherwise omit]

Then confirm: "✓ Logged to progress.md"
