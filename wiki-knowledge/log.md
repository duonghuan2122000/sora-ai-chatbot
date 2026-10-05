# Log — Wiki Sora AI Chatbot

Append-only. Định dạng: `## [YYYY-MM-DD] <thao tác> | <mô tả>`.

## [2026-10-04] init | Bootstrap wiki tại `wiki-knowledge/`; nguồn thô trỏ về `docs/` của repo
## [2026-10-04] ingest | 5 tài liệu trong `docs/`: tong-quan v1, nghiep-vu-uu-tien v2, design-system, tokens.css, spec M1
## [2026-10-04] ingest | 14 màn hình SVG M1 → tổng hợp thành [[man-hinh-m1]]
## [2026-10-05] update | Thêm quy ước đặt tên ([[quy-uoc-dat-ten]]) và style frontend SCSS ([[quy-uoc-style-frontend]]); hạn chế FOREIGN KEY vào [[thiet-ke-du-lieu-mariadb]] — đánh dấu mâu thuẫn với DDL M1 §5
## [2026-10-05] ingest | `docs/quy-uoc-code.md` → [[quy-uoc-code]]; gỡ mâu thuẫn khóa ngoại (chốt: giữ FK cho `sessions`/`auth_tokens`, cấm FK từ M4); chốt Tailwind entry CSS thuần + `base.scss` song song; ghi chú ngoại lệ vào `docs/auth/m1-tai-khoan-va-xac-thuc.md` §4
