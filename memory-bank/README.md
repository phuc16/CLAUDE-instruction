# Memory Bank Pattern

---

## Tại sao cần Memory Bank?

Claude Code có **Auto Memory** — tự động lưu patterns, corrections, preferences.
Nhưng không đủ cho complex work: không lưu decision log có reasoning, không track
progress của feature đang làm nhiều ngày, không biết bạn đang ở đâu trong task.

Memory Bank giải quyết bằng cách **externalize context ra filesystem** dưới dạng
markdown files — AI đọc được, người đọc được, commit được lên git.

---

## Cấu trúc

```
~/.claude/skills/
└── memory-bank/
    └── skill.md        ← invoke bằng /memory-bank

your-project/
└── memory/
    ├── activeContext.md      ← working state hàng ngày    [KHÔNG commit]
    ├── progress.md           ← decision log               [KHÔNG commit]
    ├── techStack.md          ← stack + config             [commit]
    └── systemPatterns.md     ← coding patterns            [commit]
```

**.gitignore:**
```gitignore
memory/activeContext.md
memory/progress.md
```

---

## Setup Skill

Skill content nằm trong file `memory-bank-skill.md` đi kèm.

```bash
mkdir -p ~/.claude/skills/memory-bank
cp memory-bank-skill.md ~/.claude/skills/memory-bank/skill.md
```

Xong. Invoke bằng `/memory-bank <argument>` trong bất kỳ Claude Code session nào.

---

## Daily Workflow — cheat sheet

| Lúc nào | Command | Claude làm gì |
|---------|---------|---------------|
| Sáng mở terminal | `/memory-bank start` | Đọc memory/, briefing 5 dòng |
| Vừa đưa ra decision | `/memory-bank decision: dùng X vì Y` | Log vào progress.md kèm Why |
| Phát hiện gotcha | `/memory-bank gotcha: Redis cần prefix order:` | Thêm vào đúng file |
| Xong 1 task | `/memory-bank done: RefundService.Initiate()` | Tick checkbox |
| Cuối ngày | `/memory-bank wrapup` | Update tất cả + print summary |
| Kiểm tra state | `/memory-bank status` | List files + flag stale |
| Project mới | `/memory-bank init` | Tạo folder + auto-fill từ codebase |

---

## Template 4 files

### `memory/activeContext.md`

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
1. Validate order ở status "delivered"
2. Call payment-service gRPC để reverse charge
3. Write refund record + update order status trong ONE transaction
4. Publish OrderRefunded event lên Kafka

## Blockers / Open Questions
- payment-service gRPC method: ReverseCharge hay CreateRefund?
  → Xem: internal/proto/payment/v1/payment.proto
- Refund sau 30 ngày có được không? → Đang chờ PM. Assume NO.
```

---

### `memory/progress.md`

```markdown
# Progress & Decisions

## Decisions Made

### 2025-06-14: Refund storage strategy
**Decision:** Separate `refunds` table, KHÔNG dùng status column trên orders.
**Why:** Một order có thể có multiple partial refunds. Cần aggregate amounts.
**Alternatives rejected:**
- JSONB column on orders: query performance kém khi aggregate
- orders_history table: quá generic, không enforce refund constraints

### 2025-06-13: gRPC vs REST cho payment-service
**Decision:** gRPC, không tạo REST endpoint mới.
**Why:** Consistency với existing pattern. payment-service đã có gRPC server.

## What Didn't Work

### Attempted: Dùng OrderRepository.Update() cho status change
**Problem:** Cập nhật ALL fields → race condition với concurrent requests.
**Solution:** Thêm UpdateStatus(ctx, id, newStatus, expectedStatus) với optimistic locking.
```

---

### `memory/techStack.md`

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
- Redis key prefix bắt buộc: "order:" → internal/infra/cache/keys.go
- Kafka consumer group: "order-service-v2" (v1 deprecated, đừng dùng)
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

### `memory/systemPatterns.md`

````markdown
# System Patterns

## Error Handling
```go
// LUÔN wrap với context: "FunctionName: %w"
return fmt.Errorf("RefundService.Initiate: %w", err)
// Custom errors tại internal/domain/errors.go
// Dùng errors.As để unwrap ở handler layer
```

## Repository Pattern
```go
// Interface ở domain layer (internal/domain/)
// Implementation ở infra layer (internal/infra/)
// Tests dùng in-memory impl, KHÔNG dùng sqlmock
```

## Context Propagation
```go
// context.Context LUÔN là tham số đầu tiên
// KHÔNG dùng context.Background() trong handlers
// Propagate: r.Context()
```

## Logging
```go
slog.InfoContext(ctx, "refund.initiated",
    "order_id", orderID,
    "amount_cents", amountCents,
)
// Format: "domain.action" — lowercase, dot-separated
// KHÔNG dùng fmt.Println, log.Printf
```

## Testing
- Unit: cùng package, không cần Docker
- Integration: build tag `//go:build integration`
- Dùng testcontainers cho PostgreSQL thật, KHÔNG sqlmock
````

---

## Hướng dẫn từng bước — từ khi khởi tạo project

### Bước 1: Cài skill — một lần duy nhất

```bash
mkdir -p ~/.claude/skills/memory-bank
cp memory-bank-skill.md ~/.claude/skills/memory-bank/skill.md
```

Xong. Skill tồn tại mãi, dùng cho mọi project.

---

### Bước 2: Khởi tạo project mới

```bash
cd your-project
claude
> /init
```

Sau khi `/init` xong, edit `CLAUDE.md` — bổ sung thủ công **Critical Business Rules**
và **Known Gotchas** mà AI không đoán được từ code.

---

### Bước 3: Khởi tạo Memory Bank

Vẫn trong session đó:

```
> /memory-bank init
```

Claude sẽ tự:
- Tạo `memory/` folder + 4 files
- Điền `techStack.md` và `systemPatterns.md` từ codebase
- Tạo skeleton cho `activeContext.md` và `progress.md`
- Append 2 dòng vào `.gitignore`

Review lại output — bổ sung những gì AI bỏ sót, đặc biệt là business rules.

---

### Bước 4: Commit initial state

```bash
git add CLAUDE.md memory/techStack.md memory/systemPatterns.md .gitignore
git commit -m "docs: init memory bank"
```

`activeContext.md` và `progress.md` không commit — đã có trong `.gitignore`.

---

### Bước 5: Ngày đầu tiên làm việc

```
> /memory-bank start
```

Lần đầu ra briefing trống. Set task mới:

```
> Tôi sẽ implement refund flow. Spec ở docs/REFUND_SPEC.md.
  Các task:
  - RefundRepository interface
  - Migration
  - RefundService.Initiate()
  - Kafka event
  - HTTP handler
```

Claude điền vào `activeContext.md`. Kiểm tra:

```
> /memory-bank status
```

---

### Bước 6: Trong ngày — khi có decision

```
> /memory-bank decision: dùng separate refunds table thay vì JSONB column
  vì một order có thể có multiple partial refunds, cần aggregate amounts
```

Nếu thiếu Why, Claude sẽ hỏi trước khi log.

---

### Bước 7: Trong ngày — khi phát hiện gotcha

```
> /memory-bank gotcha: pgx/v5 không dùng database/sql, import pgx trực tiếp
```

Claude tự quyết định file phù hợp (`techStack.md`) và append vào.

---

### Bước 8: Xong một task nhỏ

```
> /memory-bank done: RefundRepository interface
```

Claude tick checkbox trong `activeContext.md`.

---

### Bước 9: Cuối ngày

```
> /memory-bank wrapup
```

Claude update tất cả files + print summary. Đọc summary, confirm đúng, rồi:

```bash
# Chỉ commit nếu techStack hoặc systemPatterns thay đổi
git add memory/techStack.md memory/systemPatterns.md
git commit -m "docs: update memory bank"
```

---

### Bước 10: Sáng hôm sau

```bash
cd your-project
claude
> /memory-bank start
```

Nhận briefing, tiếp tục từ đúng chỗ hôm qua dừng. Không cần nhớ gì cả.

---

### Vòng lặp hàng ngày

```
Sáng:    /memory-bank start
Làm:     /memory-bank decision: ...
         /memory-bank gotcha: ...
         /memory-bank done: ...
Tối:     /memory-bank wrapup
         git add + commit (nếu cần)
```

---

## Commit định kỳ

```bash
# techStack và systemPatterns là team knowledge → commit
git add CLAUDE.md memory/techStack.md memory/systemPatterns.md
git commit -m "docs: update memory bank"

# activeContext và progress là personal state → KHÔNG commit
# (đã có trong .gitignore)
```
