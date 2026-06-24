[![EN](https://img.shields.io/badge/lang-EN-lightgrey?style=flat-square)](README.md) [![VI](https://img.shields.io/badge/lang-VI-blue?style=flat-square)](README.vi.md)

# Memory Bank — Claude Code Plugin

Externalize context làm việc của Claude ra `.claude/memory/` để tồn tại qua context reset và nhiều ngày làm việc.

## Cài đặt

```bash
# Từ thư mục gốc của repo — copy plugin vào cache
mkdir -p ~/.claude/plugins/cache/local/memory-bank/1.0.0
cp -r memory-bank/. ~/.claude/plugins/cache/local/memory-bank/1.0.0/

# Đăng ký trong installed_plugins.json (thêm entry này):
# "memory-bank@local": [{
#   "scope": "user",
#   "installPath": "/Users/<you>/.claude/plugins/cache/local/memory-bank/1.0.0",
#   "version": "1.0.0",
#   "installedAt": "<date>",
#   "lastUpdated": "<date>",
#   "gitCommitSha": null
# }]

# Sau đó trong Claude Code:
> /reload-plugins
```

## Lệnh

| Lệnh | Mô tả |
|------|-------|
| `/using-memory-bank` | Xem bản đồ skill |
| `/memory-bank:start` | Đọc tất cả memory file, xuất briefing phiên làm việc |
| `/memory-bank:decision <text>` | Ghi quyết định vào `progress.md` |
| `/memory-bank:gotcha <text>` | Ghi gotcha/blocker vào các file liên quan |
| `/memory-bank:done <task>` | Check off task, gợi ý commit message |
| `/memory-bank:wrapup` | Cập nhật cuối phiên + tóm tắt + gợi ý commit |
| `/memory-bank:status` | Kiểm tra tình trạng memory bank |
| `/memory-bank:init` | Khởi tạo từ đầu |

## Memory Files

| File | Commit | Mục đích |
|------|--------|---------|
| `techStack.md` | có | ngôn ngữ, phiên bản, lệnh dev |
| `systemPatterns.md` | có | error handling, logging, testing |
| `activeContext.md` | không | task hiện tại, checklist, bước tiếp theo, blockers |
| `progress.md` | không | nhật ký quyết định |

## Cập nhật

Sửa skill file trong `memory-bank/skills/<op>/skill.md`, sau đó copy lại:

```bash
cp -r memory-bank/. ~/.claude/plugins/cache/local/memory-bank/1.0.0/
# Sau đó /reload-plugins trong Claude Code
```
