---
title: Nguồn — Quy ước code
date: 2026-10-05
tags: [nguon, quy-uoc]
sources: [docs/quy-uoc-code.md]
---

# Nguồn — Quy ước code

Tóm tắt `docs/quy-uoc-code.md` (bổ sung 2026-10-05). File này là **nguồn sự thật** cho quy ước đặt tên, style và ràng buộc dữ liệu; thắng các tài liệu khác khi mâu thuẫn ở phạm vi này.

Nội dung đã biên soạn vào hai page concept:

- **Backend API + tên class frontend + tên đối tượng CSDL** → [[quy-uoc-dat-ten]]
- **SCSS, `base.scss`, ghép với Tailwind** → [[quy-uoc-style-frontend]]
- **Hạn chế khóa ngoại (và ngoại lệ cho bảng auth)** → [[thiet-ke-du-lieu-mariadb]] mục "Khóa ngoại"

## Ba nhóm rule, nguyên văn rút gọn

1. **Backend:** trường request/response body `snake_case`; header tự đặt tiền tố `x-sora-`.
2. **Frontend:** luôn SCSS + nested; có `base.scss` cho biến dùng chung; class HTML theo BEM tiền tố `sora-`.
3. **CSDL:** bảng/cột/PK/UK/FK `snake_case`; **hạn chế khóa ngoại** để dữ liệu linh hoạt.

## Hệ quả đã chốt

- Ngoại lệ khóa ngoại: `sessions` và `auth_tokens` giữ FK `ON DELETE CASCADE` tới `users` (M1 §4.2–4.3 đã ghi chú); từ M4 trở đi không khai FK.
- Tailwind v4 giữ entry **CSS thuần** (`main.css`); `base.scss` import song song trong `main.ts` — không gộp Tailwind vào SCSS.
- Tiền tố khóa trong CSDL: `uq_` (unique), `fk_` (foreign), `idx_` (index) — hệ thống hóa DDL M1 đã có.
