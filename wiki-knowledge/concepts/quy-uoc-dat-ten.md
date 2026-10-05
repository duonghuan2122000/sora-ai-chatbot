---
title: Quy ước đặt tên
date: 2026-10-05
tags: [quy-uoc, dat-ten, api, csdl, frontend]
sources: [docs/quy-uoc-code.md]
---

# Quy ước đặt tên

Biên soạn từ [[quy-uoc-code]] (`docs/quy-uoc-code.md`), bổ sung 2026-10-05. Nguồn này thắng các tài liệu khác khi mâu thuẫn về đặt tên.

## Backend — API

- **Trường trong request body và response body: `snake_case`.** Không camelCase, không PascalCase.
  - Ví dụ: `email_normalized`, `terms_accepted_at`, `refresh_token`, `expires_at` — khớp cột CSDL ở [[thiet-ke-du-lieu-mariadb]].
  - Go struct vẫn dùng tên export kiểu Go; đặt tag `json:"snake_case"` ở mọi trường ra/vào JSON.
- **Header tự đặt: tiền tố `x-sora-`**, viết thường.
  - Ví dụ: `x-sora-request-id`, `x-sora-conversation-id`.
  - Header chuẩn (`Authorization`, `Origin`, `X-Requested-With`) giữ nguyên tên gốc — xem [[bao-mat-ung-dung]].

## CSDL

- Bảng, tên cột, primary key, unique key, foreign key, index đều **`snake_case`**; khóa có tiền tố theo loại: `uq_` (unique) · `fk_` (foreign) · `idx_` (index). Ví dụ: `uq_users_email`, `fk_sessions_user`, `idx_sessions_user`.
- Bảng đặt tên số nhiều, tiếng Anh: `users`, `sessions`, `auth_tokens`, `documents`, `chunks`.
- Khớp với DDL hiện có của M1 ([[m1-tai-khoan-va-xac-thuc]] §5) — quy ước này hệ thống hóa điều đã làm, không phá vỡ gì.
- Ràng buộc thêm về khóa ngoại: xem [[thiet-ke-du-lieu-mariadb]] mục "Khóa ngoại".

## Frontend — tên class

- **BEM** (Block\_\_Element--Modifier), **có tiền tố `sora-`**.
  - Block: `.sora-citation-chip` · Element: `.sora-citation-chip__label` · Modifier: `.sora-citation-chip--active`.
- Tiền tố tránh đụng class của shadcn-vue/Tailwind và của thư viện ngoài.
- Liên quan: [[quy-uoc-style-frontend]] (SCSS + `base.scss`) và [[quy-tac-thiet-ke]] (§14 — thành phần UI).
- Tiện ích Tailwind vẫn dùng trực tiếp trong template; BEM áp cho class do mình viết trong SCSS, không thay thế Tailwind.
