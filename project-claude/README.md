[![EN](https://img.shields.io/badge/lang-EN-blue?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-lightgrey?style=flat-square)](README.vi.md)

# Guide: Project-level `CLAUDE.md`

Placed at the project root. Claude Code reads this **before doing anything**.
Commit it to git — shared context for the whole team.

---

## How to create: auto first, manual second

### Step 1: Auto-generate

```bash
cd /path/to/your/project
claude
> /init
```

Claude Code scans the codebase and generates a draft. **Don't write from scratch** — always edit from the draft.

### Step 2: Enrich manually

The AI can scan code structure, but doesn't know:
- Business rules in your head
- Gotchas that cost you 2 hours to debug
- The reasoning behind architectural decisions

```bash
# Find important comments scattered in the code
grep -r "IMPORTANT\|NOTE\|HACK\|FIXME\|WARNING" --include="*.go" . | head -30
```

What you find → put it in **Critical Business Rules** and **Known Gotchas**.

---

## Template

````markdown
# CLAUDE.md

## Project Overview
[Service name] for [Company]. [1-2 sentences describing purpose].
Communicates with [other services] via [protocol].

## Quick Start
```bash
make dev        # Start with docker-compose
make test       # Run all tests
make lint       # golangci-lint
make proto      # Regenerate protobuf (if applicable)
```

## Architecture
- Entry point: cmd/server/main.go
- Domain logic: internal/domain/[entity]/
- Key interfaces: internal/domain/[entity]/repository.go
- DB schema: migrations/ (golang-migrate)
- Event publishing: internal/infra/kafka/publisher.go

## Critical Business Rules
<!-- The AI CANNOT generate this section — must be written manually -->
<!-- Rules that aren't in code but determine right vs. wrong -->
- [Example: Order cannot be cancelled after status = "shipped"]
- [Example: Payment must be idempotent — use order_id as idempotency key]
- [Example: All prices stored as integer cents, never float]

## Known Gotchas
<!-- Things that cost you time — write them so the AI doesn't repeat them -->
- PostgreSQL uses UTC, always convert before storing
- Kafka consumer must commit AFTER processing, not before
- Redis keys have prefix "order:" — see internal/infra/cache/keys.go

## Test Infrastructure
```bash
# Integration tests require Docker running
docker-compose -f docker-compose.test.yml up -d
make test-integration
```
````

---

## Quality Check

Open a fresh Claude Code session and ask:

```
> How do I run integration tests?
> What database driver does this project use?
> Can I use fmt.Println for logging?
> What happens if I try to cancel a shipped order?
```

- Correct, specific answers → CLAUDE.md is working
- Generic or wrong answers → add more to the file

---

## Git

**Commit to git.** Treat it like documentation. The whole team benefits.
