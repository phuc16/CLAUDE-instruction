[![EN](https://img.shields.io/badge/lang-EN-blue?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-lightgrey?style=flat-square)](README.vi.md)

# Spec Generator

## Why do you need Spec Generator?

You receive a 2-sentence feature request on Slack. You start coding. Three days later
you discover you missed a race condition, two security edge cases, and three questions
that need PM clarification.

Spec Generator runs a **structured 4-prompt AI analysis** before you write a single line
of code — turning a vague request into an AC document, edge case checklist, risk table,
and task breakdown in under 30 minutes.

This is not AI replacing your thinking. It's AI acting as a **paranoid senior engineer
sitting next to you** — the one who asks "what if two users do this simultaneously?" and
"what happens when Redis is down?" before you find out in production.

---

## What It Generates

From one feature request, the skill produces a `docs/specs/[feature].md` containing:

| Section | What it catches |
|---------|----------------|
| **Acceptance Criteria** | Happy path, error cases, edge cases, performance requirements |
| **Edge Cases** | Concurrency, input validation, security, external dependency failures |
| **Risk Analysis** | Technical, integration, and business risks with mitigations |
| **Task Breakdown** | Backend + frontend tasks, dependencies, unknowns, PM open questions |

---

## Real Example

Feature request: *"Admin can create API keys for service accounts, with expiry date and permission scopes."*

Edge cases the AI caught that a senior engineer missed:

- **TOCTOU on key limit** — two concurrent creates both pass `count < 10`, result: 11 keys
- **Timing attack** — comparing hashed key with `==` instead of `subtle.ConstantTimeCompare`
- **Privilege escalation** — admin creates key with scopes beyond their own permissions
- **Scope contract drift** — `payments:write` vs `payment:write` typo, key created successfully but auth always fails at runtime
- **Clock skew** — `expiry_date < now()` inconsistent across instances if container clocks differ
- **Key leaked in OTel spans** — `Authorization` header appearing in distributed trace attributes

Each of these is a production bug. The analysis took 15 minutes.

---

## Structure

```
~/.claude/skills/
└── spec-generator/
    └── skill.md        ← invoke with /spec-generator

your-project/
└── docs/
    └── specs/
        └── [feature-name].md   ← generated output
```

---

## Setup

```bash
mkdir -p ~/.claude/skills/spec-generator
cp skill.MD ~/.claude/skills/spec-generator/skill.md
```

Done. Works in any Claude Code session.

---

## Usage

```
/spec-generator run          — full workflow: all 4 steps sequentially
/spec-generator ac           — Step 1 only: Acceptance Criteria
/spec-generator edge-cases   — Step 2 only: Edge Case Discovery
/spec-generator risks        — Step 3 only: Risk Analysis
/spec-generator tasks        — Step 4 only: Task Breakdown
/spec-generator review       — show annotation summary (💡 / ❌ items)
/spec-generator compile      — generate final spec document to docs/specs/
/spec-generator help         — show usage guide
```

**Every command asks for context first — nothing is assumed.**

---

## Workflow

**Full guided workflow:**
```
/spec-generator run
    ↓ annotate after each step (✅ knew it / 💡 missed it / ❌ wrong)
    ↓
/spec-generator compile  →  docs/specs/feature-name.md
```

**Pick-and-choose workflow:**
```
/spec-generator ac           ← just need AC for a quick estimate
/spec-generator risks        ← just need risk table for a review meeting
/spec-generator tasks        ← just need task breakdown for sprint planning
    ↓ run any combination
/spec-generator compile      ← compile whatever you've collected
```

---

## Works Best With

Pair with the [Memory Bank Pattern](../memory-bank/README.md) for a complete workflow:

```bash
# Planning phase
/spec-generator run          # generate spec
/spec-generator compile      # save to docs/specs/

# Implementation phase
/memory-bank start           # brief from memory files
/memory-bank decision: ...   # log choices made during implementation
/memory-bank wrapup          # end-of-day update
```

---

## When to Use / When Not to Use

**Use for:**
- Features with external dependencies (email, payment, auth, third-party APIs)
- Features with state mutations (create / update / delete)
- Features you need to estimate before having full context
- Before any PM planning meeting — arrive with questions prepared

**Skip for:**
- Trivial UI changes (rename a button, fix a typo)
- Hotfixes where speed matters more than thoroughness
- Features with highly domain-specific business rules AI cannot know
  (provide those rules as context in the prompt, then use the skill)

---

## Cheat Sheet

| When | Command | Output |
|------|---------|--------|
| Received a feature request | `/spec-generator run` | Guided 4-step analysis |
| Just need AC for estimates | `/spec-generator ac` | Acceptance criteria only |
| Just need edge cases | `/spec-generator edge-cases` | Edge case list only |
| Just need risk table | `/spec-generator risks` | Risk analysis only |
| Just need task breakdown | `/spec-generator tasks` | Task list only |
| Want to see what you missed | `/spec-generator review` | Annotated 💡/❌ list |
| Ready to document | `/spec-generator compile` | `docs/specs/feature.md` |
| Forgot the commands | `/spec-generator help` | Usage guide |
