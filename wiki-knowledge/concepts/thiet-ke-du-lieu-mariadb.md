---
title: Thiết kế dữ liệu trên MariaDB
date: 2026-10-04
tags: [du-lieu, mariadb, schema]
sources: [docs/auth/m1-tai-khoan-va-xac-thuc.md, docs/ai-chatbot-tong-quan.md]
---

# Thiết kế dữ liệu trên MariaDB

## Quy ước chung

- **ID:** kiểu `UUID` của MariaDB, sinh **UUIDv7 phía Go** (`google/uuid`).
- **Thời gian:** lưu UTC dạng `DATETIME(3)`, hiển thị theo múi giờ ở frontend.
- **Charset:** `utf8mb4` toàn bộ bảng (tiếng Việt đầy đủ).
- **Metadata linh hoạt:** để trong `JSON`; trường cần lọc nhanh thì dùng **generated column có index** (JSON của MariaDB không phải JSONB).
- **Xóa cascade:** `users` → `sessions`, `auth_tokens`; `audit_logs.user_id` đặt **NULL** để giữ bản ghi ẩn danh.
- **Đặt tên:** bảng, cột, PK, UK, FK đều `snake_case` → [[quy-uoc-dat-ten]].

## Khóa ngoại (chốt 2026-10-05, đã hết mâu thuẫn)

**Hạn chế tối đa việc khai `FOREIGN KEY`** — để dữ liệu linh hoạt (dễ tách/nhập bảng, xóa mềm, đổi kiểu khóa, không vướng thứ tự ghi). Chi tiết: [[quy-uoc-code]] §3.2.

- **Ngoại lệ có chủ đích:** `sessions` và `auth_tokens` giữ `FOREIGN KEY ... ON DELETE CASCADE` tới `users` — bảng nhỏ, vòng đời gắn chặt `users`, cascade bảo đảm xóa tài khoản không để lại phiên mồ côi. `docs/auth/m1-tai-khoan-va-xac-thuc.md` §4 đã ghi chú ngoại lệ này (cập nhật 2026-10-05).
- **Từ M4 trở đi** (`documents`, `chunks`, hội thoại, tin nhắn): **không khai khóa ngoại**. Tầng ứng dụng lo: vẫn tạo index trên cột tham chiếu (`user_id`, `document_id`), xóa theo thứ tự tường minh trong một giao dịch, và có job dọn bản ghi mồ côi.
- Kiểm thử bắt buộc phủ: xóa tài khoản, xóa tài liệu kèm chunk/vector.
- Index trên `user_id` vẫn bắt buộc vì mọi truy vấn lọc theo `uid` → [[co-lap-du-lieu-theo-user-id]].

## Bảng của M1

| Bảng | Vai trò | Điểm đáng nhớ |
|---|---|---|
| `users` | Tài khoản | `email_normalized` UNIQUE (chuẩn hóa: trim, lowercase, NFKC — **không** bỏ dấu chấm hay `+tag`); `status` ∈ active/suspended/pending_deletion; `role` ∈ user/admin; lưu `terms_accepted_at` + `terms_version` |
| `sessions` | Một dòng = một thiết bị đăng nhập | `id` chính là claim `sid` trong JWT; `refresh_hash` CHAR(64) SHA-256; `prev_refresh_hash` để phát hiện dùng lại; `expires_at` (trượt 30 ngày) và `absolute_expires_at` (cố định 90 ngày); `revoked_reason` |
| `auth_tokens` | Xác thực email, đặt lại mật khẩu | `token_hash` CHAR(64), `purpose` ∈ verify_email/reset_password; phát hành token mới cùng `purpose` thì đánh `used_at` token cũ |
| `audit_logs` | Nhật ký sự kiện xác thực | `id` BIGINT AUTO_INCREMENT (không UUID); **không bao giờ** ghi mật khẩu/token vào `metadata`; khi xóa tài khoản thì `user_id` → NULL |

Sự kiện audit: `register`, `email_verified`, `login_success`, `login_failed`, `login_locked`, `logout`, `logout_all`, `refresh_reuse_detected`, `password_changed`, `password_reset_requested`, `password_reset_completed`, `session_revoked`, `deletion_requested`, `deletion_cancelled`, `account_deleted`.

## Bảng của M4/M5 (chưa có DDL)

`documents`, `chunks` (cột `embedding VECTOR(1024)`, `VECTOR INDEX`, `FULLTEXT`), cùng bảng hội thoại, tin nhắn, phản hồi. Ràng buộc và rủi ro ở [[vector-search-mariadb]].

## Khóa Redis

| Khóa | TTL | Dùng cho |
|---|---|---|
| `login:fail:{email_norm}` | 15 phút | CAPTCHA sau 3 lần, chờ sau 5 lần |
| `login:lock:{email_norm}` | 15 phút | Khóa tạm |
| `rl:{route}:{ip}` / `rl:{route}:{key}` | theo route | Rate limit cửa sổ trượt |
| `sid:revoked:{sid}` | 15 phút | Thu hồi tức thời access token — [[xac-thuc-va-token]] |
| `mail:cooldown:{purpose}:{user_id}` | 60 giây | Giãn cách gửi lại email |
