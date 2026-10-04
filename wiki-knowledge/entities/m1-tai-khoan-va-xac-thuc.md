---
title: M1 — Tài khoản và xác thực
date: 2026-10-04
tags: [m1, module, xac-thuc]
sources: [docs/auth/m1-tai-khoan-va-xac-thuc.md, docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md]
---

# M1 — Tài khoản và xác thực

Module ưu tiên **#1** (mức 1). Có đặc tả chi tiết riêng: [[m1-spec]]. Đây là module **duy nhất đã có thiết kế màn hình**.

## Phạm vi

Đăng ký (email + mật khẩu + điều khoản) · xác thực email (gửi, gửi lại, hết hạn) · đăng nhập/đăng xuất (một hoặc mọi thiết bị) · đổi mật khẩu, quên/đặt lại mật khẩu · danh sách và thu hồi phiên đăng nhập · chống lạm dụng (thử sai, khóa tạm, CAPTCHA, rate limit) · yêu cầu xóa tài khoản (chờ 7 ngày) · audit log.

**Ngoài phạm vi:** đăng nhập Google/GitHub, 2FA (TOTP/passkey), đổi email, đăng nhập không mật khẩu. Thiết kế chừa chỗ: bảng `auth_identities` thêm sau, `users.password_hash` cho phép NULL khi đó.

## Mục tiêu chất lượng

1. Không lộ email đã có tài khoản hay chưa → [[chong-do-email]]
2. Token bị đánh cắp có tác hại thấp nhất → [[xac-thuc-va-token]]
3. Mọi bí mật chỉ lưu dạng băm
4. Người dùng luôn thấy và thu hồi được phiên của mình

## API (tiền tố `/api/v1`)

**Công khai:** `POST /auth/register` (202 luôn) · `/auth/verify-email` (204) · `/auth/verify-email/resend` (202 luôn) · `/auth/login` (200 + cookie) · `/auth/refresh` (cookie) · `/auth/logout` (204) · `/auth/forgot-password` (202 luôn) · `/auth/reset-password/check` · `/auth/reset-password`.

**Cần đăng nhập:** `GET/PATCH /me` · `POST /me/password` · `GET /me/sessions` · `DELETE /me/sessions/{id}` · `DELETE /me/sessions` (trừ phiên hiện tại) · `POST /me/deletion` · `DELETE /me/deletion`.

Lỗi thống nhất: `{ "error": { "code": "invalid_credentials", "message": "…", "retry_after": 0 } }`.

Route chat và tài liệu thêm middleware `RequireVerified` → 403 `email_not_verified`.

## Luồng đáng chú ý

- **Đăng ký (6.1):** tài khoản chưa xác thực **vẫn đăng nhập được** nhưng bị chặn chat và tải tài liệu; giao diện hiện thanh nhắc kèm nút gửi lại; tài khoản chưa xác thực sau **7 ngày** bị job dọn. Đặt lại mật khẩu thành công cũng đánh dấu email đã xác thực (người dùng đã chứng minh sở hữu hộp thư).
- **Quên mật khẩu (6.3):** token hết hạn **30 phút**, một lần dùng; thành công thì **thu hồi mọi phiên**; không tự đăng nhập lại.
- **Đổi mật khẩu (6.4):** cần mật khẩu hiện tại, mật khẩu mới không trùng cũ, mặc định thu hồi phiên khác (`sign_out_others`), luôn gửi email thông báo.
- **Xóa tài khoản (6.5):** cần mật khẩu → `status=pending_deletion`, hẹn 7 ngày → đăng nhập lại trong thời gian chờ hiện nút **Hủy xóa** → job hằng ngày xóa chunk/vector (M4/M5), file MinIO, hội thoại, rồi `users` (cascade `sessions`, `auth_tokens`) và **ẩn danh** `audit_logs`.

## Kế hoạch triển khai (§13)

| Giai đoạn | Nội dung | Ước lượng |
|---|---|---|
| **A. Lõi (MVP)** | `users`, `sessions`, đăng ký/đăng nhập/refresh xoay vòng/đăng xuất, middleware, đổi mật khẩu; màn hình 01, 04, 11 | 6–8 ngày |
| **B. Email** | Asynq, mẫu email, xác thực email, quên/đặt lại mật khẩu, `RequireVerified`; màn hình 05–10 | 4–6 ngày |
| **C. Chống lạm dụng** | Rate limit, đếm sai, khóa tạm, Turnstile, danh sách mật khẩu bị lộ; màn hình 02, 03 | 3–4 ngày |
| **D. Phiên và dữ liệu** | Danh sách/thu hồi phiên, audit log, xóa tài khoản + job; màn hình 12, 13 | 4–5 ngày |
| **E. Hoàn thiện (mức 3)** | Google/GitHub, email "thiết bị mới", TOTP/passkey | tùy chọn |

Ước lượng cho 1 backend + 1 frontend song song.

**Giai đoạn B nên làm trong MVP** dù v2 xếp xác thực email ở mức 2 (#10) — vì sản phẩm mở đăng ký công khai, đúng như ghi chú trong [[nghiep-vu-uu-tien-v2]].

## Kiểm thử (§12)

Tám nhóm: đăng ký · xác thực email · đăng nhập · refresh · thu hồi · **phân quyền (IDOR — không xem/thu hồi được phiên của người khác)** · xóa tài khoản · rate limit. Cộng SAST (`gosec`, `govulncheck`), kiểm thử tải argon2id, kiểm thử nhiều tab/mạng chập chờn, kiểm tra SPF/DKIM/DMARC trên Gmail/Outlook/Yahoo.

## Cấu trúc code

Go: `internal/auth/` (handler, service, repo, password, token, mailer, captcha, ratelimit, middleware, audit) + `internal/user/` + `internal/jobs/` (purge unverified, xóa tài khoản đến hạn).
Vue: xem [[mo-hinh-token-frontend]] và [[man-hinh-m1]].
