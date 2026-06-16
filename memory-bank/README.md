[![EN](https://img.shields.io/badge/lang-EN-blue?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-lightgrey?style=flat-square)](README.vi.md)

# Memory Bank Pattern

## Why do you need Memory Bank?

Claude Code has **Auto Memory** — it automatically saves patterns, corrections, and preferences.
But it's not enough for complex work: it doesn't log decisions with reasoning, doesn't track
progress on features spanning multiple days, and doesn't know where you are in a task.

Memory Bank solves this by **externalizing context to the filesystem** as markdown files —
readable by AI, readable by humans, committable to git.

---

## Structure

```
~/.claude/skills/
└── memory-bank/
    └── skill.md        ← invoke with /memory-bank

your-project/
└── .claude/
    └── memory/
        ├── activeContext.md      ← daily working state      [do NOT commit]
        ├── progress.md           ← decision log             [do NOT commit]
        ├── techStack.md          ← stack + config           [commit]
        └── systemPatterns.md     ← coding patterns          [commit]
```

**Two files are not committed** because they are personal state — each person has their own
context, and committing them causes merge conflicts with teammates:

```gitignore
.claude/memory/activeContext.md   # "what am I working on today"
.claude/memory/progress.md        # "what decisions did I make this session"
```

The other two are committed because they are shared team knowledge:

```gitignore
# DO NOT ignore — commit to git
.claude/memory/techStack.md       # stack + config the whole team needs
.claude/memory/systemPatterns.md  # coding patterns the whole team follows
```

---

## Setup Skill

The skill content is in the `skill.MD` file included here.

```bash
mkdir -p ~/.claude/skills/memory-bank
cp skill.MD ~/.claude/skills/memory-bank/skill.md
```

Done. Invoke with `/memory-bank <argument>` in any Claude Code session.

---

## Daily Workflow — cheat sheet

| When | Command | What Claude does |
|------|---------|-----------------|
| Morning | `/memory-bank start` | Reads .claude/memory/, 5-line briefing |
| After a decision | `/memory-bank decision: use X because Y` | Logs to progress.md with Why |
| Found a gotcha | `/memory-bank gotcha: Redis needs prefix order:` | Appends to the right file |
| Finished a task | `/memory-bank done: RefundService.Initiate()` | Ticks checkbox + suggests commit |
| End of day | `/memory-bank wrapup` | Updates everything + prints summary |
| Check state | `/memory-bank status` | Lists files + flags stale |
| New project | `/memory-bank init` | Creates folder + auto-fills from codebase |

---

## Template — 4 files

### `.claude/memory/activeContext.md`

```markdown
# Active Context

Last updated: 2025-06-15 14:30

## Current Task
Implementing refund flow for orders.
Spec: docs/specs/REFUND_SPEC.md

## Exactly Where I Am
- [x] RefundRepository interface → internal/domain/order/refund.go
- [x] PostgreSQL migration → migrations/012_add_refunds_table.sql
- [ ] RefundService.Initiate() — IN PROGRESS
- [ ] Kafka event publishing after refund created
- [ ] HTTP handler: POST /orders/{id}/refunds

## Current File Being Worked On
internal/app/refund_service.go — method Initiate() is incomplete

## Immediate Next Step
1. Validate order at status "delivered"
2. Call payment-service gRPC to reverse charge
3. Write refund record + update order status in ONE transaction
4. Publish OrderRefunded event to Kafka

## Blockers / Open Questions
- payment-service gRPC method: ReverseCharge or CreateRefund?
  → See: internal/proto/payment/v1/payment.proto
- Can we refund after 30 days? → Waiting on PM. Assume NO.
```

---

### `.claude/memory/progress.md`

```markdown
# Progress & Decisions

## Decisions Made

### 2025-06-14: Refund storage strategy
**Decision:** Separate `refunds` table, NOT a status column on orders.
**Why:** An order can have multiple partial refunds. Need to aggregate amounts.
**Alternatives rejected:**
- JSONB column on orders: poor query performance when aggregating
- orders_history table: too generic, doesn't enforce refund constraints

### 2025-06-13: gRPC vs REST for payment-service
**Decision:** gRPC, not a new REST endpoint.
**Why:** Consistency with existing pattern. payment-service already has a gRPC server.

## What Didn't Work

### Attempted: Using OrderRepository.Update() for status change
**Problem:** Updates ALL fields → race condition with concurrent requests.
**Solution:** Added UpdateStatus(ctx, id, newStatus, expectedStatus) with optimistic locking.
```

---

### `.claude/memory/techStack.md`

```markdown
# Tech Stack

## Core
- Go 1.23.4
- PostgreSQL 16 (pgx/v5 — NOT database/sql)
- Redis 7.2 (go-redis/v9)
- Kafka 3.6 (confluent-kafka-go)

## Key Libraries
| Library                | Version | Usage                   |
|------------------------|---------|-------------------------|
| pgx/v5                 | v5.7.1  | DB driver, pool         |
| go-redis/v9            | v9.7.0  | Cache, distributed lock |
| confluent-kafka-go     | v2.6.0  | Producer/consumer       |
| google.golang.org/grpc | v1.69.0 | gRPC client/server      |
| slog                   | stdlib  | Structured logging      |

## Non-obvious Config
- PgPool max connections: 20 → internal/infra/db/pool.go
- Redis key prefix required: "order:" → internal/infra/cache/keys.go
- Kafka consumer group: "order-service-v2" (v1 deprecated, do not use)
- gRPC max message size: 4MB default

## Local Development
```bash
make dev        # docker-compose up + health checks
make psql       # Connect to local DB
make redis-cli  # Connect to local Redis
```

## Environment Variables
Required: DATABASE_URL, REDIS_ADDR, KAFKA_BROKERS, PAYMENT_SERVICE_ADDR
Optional: LOG_LEVEL (default: info), PORT (default: 8080)
```

---

### `.claude/memory/systemPatterns.md`

````markdown
# System Patterns

## Error Handling
```go
// ALWAYS wrap with context: "FunctionName: %w"
return fmt.Errorf("RefundService.Initiate: %w", err)
// Custom errors at internal/domain/errors.go
// Use errors.As to unwrap at handler layer
```

## Repository Pattern
```go
// Interface at domain layer (internal/domain/)
// Implementation at infra layer (internal/infra/)
// Tests use in-memory impl, NOT sqlmock
```

## Context Propagation
```go
// context.Context is ALWAYS the first parameter
// Do NOT use context.Background() in handlers
// Propagate: r.Context()
```

## Logging
```go
slog.InfoContext(ctx, "refund.initiated",
    "order_id", orderID,
    "amount_cents", amountCents,
)
// Format: "domain.action" — lowercase, dot-separated
// Do NOT use fmt.Println, log.Printf
```

## Testing
- Unit: same package, no Docker needed
- Integration: build tag `//go:build integration`
- Use testcontainers for real PostgreSQL, NOT sqlmock
````

---

## Step-by-step Guide

### Step 1: Install the skill — once only

```bash
mkdir -p ~/.claude/skills/memory-bank
cp skill.MD ~/.claude/skills/memory-bank/skill.md
```

Done. The skill persists forever, works for every project.

---

### Step 2: Initialize a new project

```bash
cd your-project
claude
> /init
```

After `/init`, edit `CLAUDE.md` — manually add **Critical Business Rules**
and **Known Gotchas** that the AI can't infer from code.

---

### Step 3: Initialize Memory Bank

Still in the same session:

```
> /memory-bank init
```

Claude will:
- Create `.claude/memory/` folder + 4 files
- Fill in `techStack.md` and `systemPatterns.md` from the codebase
- Create skeletons for `activeContext.md` and `progress.md`

When done, Claude outputs:

```
✓ Memory Bank initialized.

Add personal state files to .gitignore?
(activeContext.md and progress.md — these are yours alone, not for teammates)

Y (recommended) — append to .gitignore (prevents merge conflicts when teammates each update their own "current task")
                  These files track your personal working state — not meant to be shared.
n — skip (fine for a personal/solo repo, or if you prefer to manage .gitignore yourself)

---
Review these two files once done — they're what I detected from code:

- .claude/memory/techStack.md — ...
- .claude/memory/systemPatterns.md — ...

Add any business rules or gotchas I couldn't detect from code.
```

Choose **Y** (recommended) to let Claude append to `.gitignore`. Choose **n** for personal projects or if you manage `.gitignore` yourself.

---

### Step 4: Commit initial state

```bash
git add CLAUDE.md .claude/memory/techStack.md .claude/memory/systemPatterns.md .gitignore
git commit -m "docs: init memory bank"
```

`activeContext.md` and `progress.md` are not committed — personal state only.
If you chose Y above, `.gitignore` already handles this.

---

### Step 5: First day of work

```
> /memory-bank start
```

First time outputs an empty briefing. Set a new task:

```
> I'll implement the refund flow. Spec at docs/REFUND_SPEC.md.
  Tasks:
  - RefundRepository interface
  - Migration
  - RefundService.Initiate()
  - Kafka event
  - HTTP handler
```

Claude fills in `activeContext.md` then confirms "✓ Task logged. Ready to start — just say go."
Only after you confirm does Claude begin working.

---

### Step 6: During the day — when you make a decision

```
> /memory-bank decision: use separate refunds table instead of JSONB column
  because an order can have multiple partial refunds, need to aggregate amounts
```

If Why is missing, Claude will ask before logging.

---

### Step 7: During the day — when you find a gotcha

```
> /memory-bank gotcha: pgx/v5 doesn't use database/sql, import pgx directly
```

Claude decides which file to append to (`techStack.md`) and adds it.

---

### Step 8: Finished a small task

```
> /memory-bank done: RefundRepository interface
```

Claude ticks the checkbox in `activeContext.md` and suggests a commit message.

---

### Step 9: End of day

```
> /memory-bank wrapup
```

Claude updates all files + prints a summary. Review it, then:

```bash
# Only commit if techStack or systemPatterns changed
git add .claude/memory/techStack.md .claude/memory/systemPatterns.md
git commit -m "docs: update memory bank"
```

---

### Step 10: Next morning

```bash
cd your-project
claude
> /memory-bank start
```

Get briefing, continue from exactly where you left off. No need to remember anything.

---

### Daily loop

```
Morning:  /memory-bank start
During:   /memory-bank decision: ...
          /memory-bank gotcha: ...
          /memory-bank done: ...
Evening:  /memory-bank wrapup
          git add + commit (if needed)
```

---

## Periodic commits

```bash
# techStack and systemPatterns are team knowledge → commit
git add CLAUDE.md .claude/memory/techStack.md .claude/memory/systemPatterns.md
git commit -m "docs: update memory bank"

# activeContext and progress are personal state → do NOT commit
# (already in .gitignore)
```
