[![EN](https://img.shields.io/badge/lang-EN-lightgrey?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-blue?style=flat-square)](README.vi.md)

# Spec Generator

## Tại sao cần Spec Generator?

Bạn nhận được 2 câu mô tả feature trên Slack. Bạn bắt đầu code. Ba ngày sau phát hiện
bỏ sót một race condition, hai security edge case, và ba câu hỏi cần PM xác nhận.

Spec Generator chạy **4-prompt AI analysis có cấu trúc** trước khi bạn viết một dòng
code — biến yêu cầu mơ hồ thành AC document, edge case checklist, risk table, và task
breakdown trong dưới 30 phút.

Đây không phải AI thay thế tư duy của bạn. Đây là AI đóng vai **senior engineer paranoid
ngồi cạnh bạn** — người luôn hỏi "nếu hai user làm điều này cùng lúc thì sao?" và "nếu
Redis down thì sao?" trước khi bạn phát hiện ở production.

---

## Output tạo ra là gì

Từ một feature request, skill tạo ra `docs/specs/[feature].md` gồm:

| Section | Bắt được gì |
|---------|------------|
| **Acceptance Criteria** | Happy path, error cases, edge cases, performance requirements |
| **Edge Cases** | Concurrency, input validation, security, external dependency failures |
| **Risk Analysis** | Technical, integration, business risks kèm mitigations |
| **Task Breakdown** | Backend + frontend tasks, dependencies, unknowns, PM open questions |

---

## Ví dụ thực tế

Feature request: *"Admin muốn có thể tạo API key cho service accounts, với expiry date và permission scopes."*

Edge cases AI phát hiện mà senior engineer bỏ sót:

- **TOCTOU trên key limit** — hai request tạo đồng thời cùng pass `count < 10`, kết quả: 11 keys
- **Timing attack** — so sánh hashed key bằng `==` thay vì `subtle.ConstantTimeCompare`
- **Privilege escalation** — admin tạo key với scopes vượt quá quyền của chính mình
- **Scope contract drift** — `payments:write` vs `payment:write` typo, key tạo thành công nhưng auth luôn fail ở runtime
- **Clock skew** — `expiry_date < now()` không nhất quán giữa các instances nếu container clocks lệch nhau
- **Key leak trong OTel spans** — `Authorization` header xuất hiện trong distributed trace attributes

Mỗi cái này là một production bug. Toàn bộ analysis mất 15 phút.

---

## Cấu trúc

```
~/.claude/skills/
└── spec-generator/
    └── skill.md        ← invoke bằng /spec-generator

your-project/
└── docs/
    └── specs/
        └── [feature-name].md   ← output được tạo
```

---

## Setup

```bash
mkdir -p ~/.claude/skills/spec-generator
cp skill.MD ~/.claude/skills/spec-generator/skill.md
```

Xong. Dùng được trong mọi Claude Code session.

---

## Cách dùng

```
/spec-generator run          — full workflow: cả 4 steps tuần tự
/spec-generator ac           — chỉ Step 1: Acceptance Criteria
/spec-generator edge-cases   — chỉ Step 2: Edge Case Discovery
/spec-generator risks        — chỉ Step 3: Risk Analysis
/spec-generator tasks        — chỉ Step 4: Task Breakdown
/spec-generator review       — hiện annotation summary (💡 / ❌ items)
/spec-generator compile      — tạo spec document vào docs/specs/
/spec-generator help         — hiện usage guide
```

**Mọi command đều hỏi context trước — không assume bất cứ điều gì.**

---

## Workflow

**Full guided workflow:**
```
/spec-generator run
    ↓ annotate sau mỗi step (✅ biết rồi / 💡 chưa nghĩ đến / ❌ sai)
    ↓
/spec-generator compile  →  docs/specs/feature-name.md
```

**Pick-and-choose workflow:**
```
/spec-generator ac           ← chỉ cần AC để estimate nhanh
/spec-generator risks        ← chỉ cần risk table cho review meeting
/spec-generator tasks        ← chỉ cần task breakdown cho sprint planning
    ↓ chạy bất kỳ tổ hợp nào
/spec-generator compile      ← compile những gì đã có
```

---

## Kết hợp tốt nhất với

Dùng cùng [Memory Bank Pattern](../memory-bank/README.md) để có workflow hoàn chỉnh:

```bash
# Giai đoạn planning
/spec-generator run          # generate spec
/spec-generator compile      # lưu vào docs/specs/

# Giai đoạn implementation
/memory-bank start           # đọc memory files, lấy briefing
/memory-bank decision: ...   # log quyết định trong lúc implement
/memory-bank wrapup          # update cuối ngày
```

---

## Khi nào dùng / Khi nào không dùng

**Nên dùng:**
- Feature có external dependencies (email, payment, auth, third-party API)
- Feature có state mutations (create / update / delete)
- Feature cần estimate trước khi có đủ context
- Trước PM planning meeting — vào họp với danh sách câu hỏi đã chuẩn bị

**Bỏ qua khi:**
- UI change nhỏ (đổi tên button, sửa typo)
- Hotfix cần tốc độ hơn là thoroughness
- Feature có business rules domain-specific mà AI không thể biết
  (trong trường hợp này: cung cấp rules đó làm context, rồi mới chạy skill)

---

## Cheat Sheet

| Khi nào | Command | Output |
|---------|---------|--------|
| Nhận feature request | `/spec-generator run` | Guided 4-step analysis |
| Chỉ cần AC để estimate | `/spec-generator ac` | Acceptance criteria only |
| Chỉ cần edge cases | `/spec-generator edge-cases` | Edge case list only |
| Chỉ cần risk table | `/spec-generator risks` | Risk analysis only |
| Chỉ cần task breakdown | `/spec-generator tasks` | Task list only |
| Muốn xem mình bỏ sót gì | `/spec-generator review` | Annotated 💡/❌ list |
| Sẵn sàng document | `/spec-generator compile` | `docs/specs/feature.md` |
| Quên commands | `/spec-generator help` | Usage guide |
