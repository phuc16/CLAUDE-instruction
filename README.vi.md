[![EN](https://img.shields.io/badge/lang-EN-lightgrey?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-blue?style=flat-square)](README.vi.md)

# Hướng dẫn Claude Code

Các hướng dẫn thực tế để tận dụng Claude Code trong công việc hàng ngày.

---

## Nội dung

### [Memory Bank Pattern](memory-bank/README.vi.md)
Externalize context làm việc của Claude ra `.claude/memory/` — các markdown file tồn tại qua context reset và nhiều ngày làm việc. Đóng gói dưới dạng Claude Code plugin với các lệnh `/memory-bank:start`, `/memory-bank:done`, `/memory-bank:decision`, `/memory-bank:wrapup` và nhiều hơn nữa.

### [Project-level CLAUDE.md](project-claude/README.vi.md)
Cách viết `CLAUDE.md` thực sự hiệu quả: những gì auto-generate được bằng `/init`, những gì phải viết thủ công (business rules, gotchas, quyết định kiến trúc), và cách kiểm tra chất lượng.

---

## Bắt đầu nhanh

```bash
# 1. Clone repo này (một lần)
git clone https://github.com/phucth/CLAUDE-instruction ~/.claude-instruction

# 2. Cài Memory Bank plugin (một lần, dùng cho mọi project)
# Copy plugin vào cache và đăng ký — xem memory-bank/README.vi.md để biết chi tiết
# Sau đó trong Claude Code:
> /reload-plugins

# 3. Trong bất kỳ project nào
cd your-project
claude
> /init                    # generate CLAUDE.md
> /memory-bank:init        # setup .claude/memory/
```
