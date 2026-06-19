---
description: Spec Generator — turn a vague feature request into a full technical spec (AC, edge cases, risks, task breakdown) using 4 sequential AI prompts
argument-hint: <run|ac|edge-cases|risks|tasks|review|compile|help>
---

You are running the Spec Generator workflow for this feature request.
Act based on $ARGUMENTS:

---

## Shared: context collection

Whenever context is needed (feature, stack, scale), always ask ALL THREE — never assume any:

```
Before I start, I need 3 things:

1. Feature description (1–3 sentences — what does it do, who uses it?)
2. Stack (e.g. Go + PostgreSQL + Redis / Next.js + TypeScript)
3. Scale (e.g. <10,000 users / 1M DAU / internal tool)
```

Wait for all 3 answers before proceeding. If any answer is missing or unclear, ask again for that specific item.

---

## run

Collect context (see Shared section above), then run all 4 steps sequentially.
Pause after each step for user annotation before proceeding to the next.

**Step 1 → Step 2 → Step 3 → Step 4** (same content as the individual commands below).

After Step 4, confirm: "✓ All 4 prompts complete. Run `/spec-generator compile` to generate the final spec document."

---

## ac

Collect context (see Shared section above).

Then generate acceptance criteria for the feature with:
- Happy path (Given/When/Then format)
- Error cases
- Edge cases
- Performance requirements

Output format: numbered AC-01, AC-02... grouped by category.

After generating, ask the user to annotate each item:
- ✅ Already knew this
- 💡 Didn't think of this — important
- ❌ Wrong for my context — [reason]

Wait for annotations, then confirm: "✓ AC done. Run `/spec-generator edge-cases` to continue, or `/spec-generator compile` if you have all you need."

---

## edge-cases

Collect context (see Shared section above).

Then generate additional edge cases grouped by:
- Concurrency
- Input Validation
- Security
- External Dependencies

Format: bullet list only, no explanation. User will prioritize themselves.

After generating, ask user to annotate:
- ✅ Already knew this
- 💡 Didn't think of this — important
- ❌ Wrong for my context — [reason]

Wait for annotations, then confirm: "✓ Edge cases done. Run `/spec-generator risks` to continue, or `/spec-generator compile` if you have all you need."

---

## risks

Collect context (see Shared section above).

Then act as a pessimistic senior engineer doing pre-mortem analysis.
List top 5 risks in 3 categories:
- Technical risks
- Integration risks
- Business risks

Table format: Risk | Likelihood (H/M/L) | Impact (H/M/L) | Mitigation

After generating, ask user to annotate:
- ✅ Already knew this
- 💡 Didn't think of this — important
- ❌ Wrong for my context — [reason]

Wait for annotations, then confirm: "✓ Risk analysis done. Run `/spec-generator tasks` to continue, or `/spec-generator compile` if you have all you need."

---

## tasks

Collect context (see Shared section above).

Then break the feature into implementation tasks.
For each task:
- Description
- Dependencies (what must be done first)
- Unknowns (what needs investigation before starting)

Include BOTH backend and frontend tasks if the feature has a UI.
Flag all ambiguities that need PM clarification as a summary table at the end:
| # | Question | Blocks | Priority (H/M/L) |

After generating, confirm: "✓ Task breakdown done. Run `/spec-generator compile` to generate the final spec document."

---

## review

Show the current annotation summary from all 4 steps:
- Items marked 💡 (didn't think of — must implement)
- Items marked ❌ (wrong for context — with reasons)
- Items marked ✅ (known — can skip in review)

Group by step. Output as a clean checklist.

---

## compile

Generate the final spec document and save to `docs/specs/[feature-name].md`.

The document must include:

```markdown
# Feature: [Name]
Date: [today]
Status: Draft — pending PM clarification

---

## Context
[Stack, scale, 1-paragraph description]

---

## Acceptance Criteria
[All AC items from Step 1, updated with user annotations]

---

## Edge Cases (confirmed)
### Must implement (marked 💡):
[List only items user marked as 💡 from Steps 1 and 2]

---

## Risks & Mitigations
[All 3 risk tables from Step 3]

---

## Task Breakdown

### Backend
[Tasks from Step 4 — backend]

### Frontend
[Tasks from Step 4 — frontend, if applicable]

### Dependency Graph
[ASCII dependency graph]

---

## Open Questions for PM
[Summary table from Step 4 PM clarifications]
```

After saving, confirm:
"✓ Spec saved to docs/specs/[feature-name].md

This document is ready to:
- Share with PM for clarification on open questions
- Use as implementation reference
- Paste into /memory-bank start as the current task spec"

---

## help

Output this usage guide:

```
/spec-generator run          — full workflow: all 4 steps sequentially
/spec-generator ac           — Step 1 only: Acceptance Criteria
/spec-generator edge-cases   — Step 2 only: Edge Case Discovery
/spec-generator risks        — Step 3 only: Risk Analysis
/spec-generator tasks        — Step 4 only: Task Breakdown
/spec-generator review       — show annotation summary (💡 / ❌ items)
/spec-generator compile      — generate final spec document to docs/specs/
/spec-generator help         — show this guide
```

**Full workflow (guided):**
```
/spec-generator run
    ↓ annotate after each step
    ↓
/spec-generator compile
    ↓
docs/specs/feature-name.md
```

**Pick-and-choose workflow:**
```
/spec-generator ac           ← just need AC for a quick estimate
/spec-generator risks        ← just need risk table for a review meeting
/spec-generator tasks        ← just need task breakdown for sprint planning
    ↓ (run any combination)
/spec-generator compile      ← compile whatever you've collected
```

**Every command asks for context first — nothing is assumed.**

**When to use:**
- Any feature with external dependencies (email, payment, auth)
- Any feature with state mutations (create/update/delete)
- Any feature you need to estimate before having full context
- Before any PM planning meeting

**When NOT to use:**
- Trivial UI changes (rename a button, fix a typo)
- Hotfixes where speed > thoroughness
- Features with highly domain-specific business rules AI can't know
  (in this case: use any command but provide the rules in context)
