[![EN](https://img.shields.io/badge/lang-EN-lightgrey?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-blue?style=flat-square)](README.vi.md)

# Hướng dẫn Claude Code

Các hướng dẫn thực tế để tận dụng Claude Code trong công việc hàng ngày.

---

## Nội dung

### [Memory Bank Pattern](memory-bank/README.vi.md)
Externalize context làm việc của Claude ra `.claude/memory/` — các markdown file tồn tại qua context reset và nhiều ngày làm việc. Bao gồm skill cài sẵn (`/memory-bank`) với các lệnh `start`, `done`, `decision`, `wrapup` và nhiều hơn nữa.

### [Project-level CLAUDE.md](project-claude/README.vi.md)
Cách viết `CLAUDE.md` thực sự hiệu quả: những gì auto-generate được bằng `/init`, những gì phải viết thủ công (business rules, gotchas, quyết định kiến trúc), và cách kiểm tra chất lượng.

---

## Bắt đầu nhanh

```bash
# 1. Cài Memory Bank skill (một lần, dùng cho mọi project)
mkdir -p ~/.claude/skills/memory-bank
cp memory-bank/skill.MD ~/.claude/skills/memory-bank/skill.md

# 2. Trong bất kỳ project nào
cd your-project
claude
> /init                  # generate CLAUDE.md
> /memory-bank init      # setup .claude/memory/
```
