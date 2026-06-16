[![EN](https://img.shields.io/badge/lang-EN-lightgrey?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-blue?style=flat-square)](README.vi.md)

# Hướng dẫn: Project-level `CLAUDE.md`

File đặt tại root project. Claude Code đọc đây **trước khi làm bất cứ thứ gì**.
Commit lên git — shared context cho cả team.

---

## Cách tạo: auto trước, manual sau

### Bước 1: Auto-generate

```bash
cd /path/to/your/project
claude
> /init
```

Claude Code scan codebase và generate draft. **Đừng viết từ đầu** — luôn edit từ draft.

### Bước 2: Enrich thủ công

AI scan được cấu trúc code, nhưng không biết:
- Business rules ẩn trong đầu bạn
- Gotcha từng làm bạn mất 2 tiếng debug
- Lý do đằng sau các quyết định kiến trúc

```bash
# Tìm comment quan trọng đang nằm rải rác trong code
grep -r "IMPORTANT\|NOTE\|HACK\|FIXME\|WARNING" --include="*.go" . | head -30
```

Những gì tìm được → đưa vào **Critical Business Rules** và **Known Gotchas**.

---

## Template

````markdown
# CLAUDE.md

## Project Overview
[Tên service] cho [Company]. [1-2 câu mô tả purpose].
Giao tiếp với [services khác] qua [protocol].

## Quick Start
```bash
make dev        # Start với docker-compose
make test       # Run all tests
make lint       # golangci-lint
make proto      # Regenerate protobuf (nếu có)
```

## Architecture
- Entry point: cmd/server/main.go
- Domain logic: internal/domain/[entity]/
- Key interfaces: internal/domain/[entity]/repository.go
- DB schema: migrations/ (golang-migrate)
- Event publishing: internal/infra/kafka/publisher.go

## Critical Business Rules
<!-- AI KHÔNG THỂ tự generate phần này — phải viết thủ công -->
<!-- Những rule không có trong code nhưng quyết định đúng/sai -->
- [Ví dụ: Order không thể cancel sau status = "shipped"]
- [Ví dụ: Payment phải idempotent — dùng order_id làm idempotency key]
- [Ví dụ: Toàn bộ giá tiền lưu integer cents, không dùng float]

## Known Gotchas
<!-- Những thứ từng làm bạn mất thời gian — viết để AI không lặp lại -->
- PostgreSQL dùng UTC, luôn convert trước khi store
- Kafka consumer phải commit AFTER processing, không phải before
- Redis keys có prefix "order:" — xem internal/infra/cache/keys.go

## Test Infrastructure
```bash
# Integration tests cần Docker running
docker-compose -f docker-compose.test.yml up -d
make test-integration
```
````

---

## Kiểm tra chất lượng

Mở Claude Code fresh session, hỏi:

```
> How do I run integration tests?
> What database driver does this project use?
> Can I use fmt.Println for logging?
> What happens if I try to cancel a shipped order?
```

- Trả lời đúng, cụ thể → CLAUDE.md đang hoạt động
- Trả lời generic hoặc sai → bổ sung thêm vào file

---

## Git

**Commit lên git.** Treat như documentation. Cả team benefit.
