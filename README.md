# Japanese SRS Tracker

Hệ thống học tiếng Nhật theo phương pháp **Spaced Repetition** (SM-2 rút gọn). Source of truth: [`srs.md`](./srs.md).

## Cách hoạt động

1. **Thêm từ mới**: Nghiep chat với Claude (bất kỳ session nào) → Claude append item vào `srs.md` với `Stage = 0`, `Next Review = ngày mai` (Asia/Tokyo).
2. **Daily review**: Scheduled remote agent chạy **10h sáng Tokyo (1h UTC), daily 7/7** → đọc `srs.md` → lọc items có `Next Review <= today` → generate 2 câu IT-themed (mức dễ-thường, dùng được trong hội thoại) → POST qua Slack Workflow webhook vào channel `#times-nghiep` → advance `Stage` và update `Next Review` → commit + push.
3. **Khi quên**: Nghiep edit `srs.md` thủ công (hoặc nhờ Claude) — đổi `Stage` về 0, `Next Review` về ngày mai để học lại từ đầu.

## Stage progression

| Stage | Sau review → Next Review = today + |
|-------|------------------------------------|
| 0 (new) → 1 | 1 ngày |
| 1 → 2 | 3 ngày |
| 2 → 3 | 7 ngày |
| 3 → 4 | 14 ngày |
| 4 → 5 | 30 ngày |
| 5 → 6 | 60 ngày |
| 6 → 7 | 120 ngày |
| 7 | `retired` (graduated, không review nữa) |

## Format `srs.md`

```
| ID | JP | Reading | Meaning (VI) | Type | Added | Stage | Next Review |
```

- `ID`: số nguyên tăng dần (không tái sử dụng)
- `JP`: kanji/kana
- `Reading`: hiragana hoặc romaji
- `Meaning (VI)`: nghĩa tiếng Việt ngắn gọn
- `Type`: `vocab` | `grammar` | `phrase`
- `Added`: YYYY-MM-DD (Asia/Tokyo)
- `Stage`: 0–7
- `Next Review`: YYYY-MM-DD (Asia/Tokyo), hoặc `retired` khi `Stage = 7`

## Câu ví dụ IT-themed

Không lưu trong file — agent generate fresh mỗi lần review để não tiếp xúc đa dạng. Context: developer daily life (code review, deploy, debug, PR, meeting, standup, etc.). Level: dễ-conversational, không phải JLPT N1.

## Slack delivery

- Mechanism: **Slack Workflow Builder webhook** (không phải MCP/DM)
- Workspace: `88oct.slack.com`
- Channel: **`#times-nghiep`**
- Identity: workflow bot → notification hoạt động bình thường (post từ self qua MCP không noti được — đây là lý do dùng workflow)
- Webhook URL: lưu trong prompt routine (private), KHÔNG commit vào repo

## Quản lý routine

Dashboard: https://claude.ai/code/routines
