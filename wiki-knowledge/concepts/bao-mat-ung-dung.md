---
title: Bảo mật ứng dụng — cookie, header, prompt injection
date: 2026-10-04
tags: [bao-mat, m6]
sources: [docs/auth/m1-tai-khoan-va-xac-thuc.md, docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md, docs/ai-chatbot-tong-quan.md]
---

# Bảo mật ứng dụng

## Ba quy tắc bất di bất dịch

1. **Cô lập dữ liệu theo `uid` từ middleware**, không nhận `user_id` từ client → [[co-lap-du-lieu-theo-user-id]].
2. **Prompt người dùng là dữ liệu không tin cậy** → mục dưới.
3. **Bí mật chỉ lưu dạng băm** — mật khẩu argon2id, refresh token SHA-256, token email SHA-256. **Không** ghi mật khẩu/token vào log hay `audit_logs.metadata`.

## Prompt injection

Prompt cấu hình do người dùng viết (M3) **và** nội dung tài liệu đều là nguồn injection.

- Prompt người dùng đặt trong **khối riêng, sau quy tắc hệ thống**, có giới hạn độ dài, **không** được ghi đè quy tắc an toàn/trích dẫn.
- **Phạm vi tài liệu do backend quyết theo `uid`**, không phụ thuộc nội dung prompt — đây là điều biến prompt injection từ lỗ hổng đọc chéo dữ liệu thành chỉ là sai lệch nội dung trả lời.
- Xem [[rag]] cho vị trí ghép ngữ cảnh.

## Cookie, CSRF, header

- Cookie refresh: `HttpOnly; Secure; SameSite=Strict; Path=/api/v1/auth`, tiền tố `__Secure-`.
- Chỉ `/auth/refresh` và `/auth/logout` dùng cookie → thêm kiểm tra header `Origin` thuộc danh sách cho phép và yêu cầu `X-Requested-With`. Các route còn lại dùng `Authorization: Bearer` nên không bị CSRF.
- Header tự đặt của hệ thống mang tiền tố `x-sora-` → [[quy-uoc-dat-ten]].
- Header: `Strict-Transport-Security`, `Content-Security-Policy` chặt (không inline script), `X-Content-Type-Options: nosniff`, `Referrer-Policy: no-referrer`, `Cache-Control: no-store` cho `/auth/*` và `/me/*`.
- CORS: chỉ đúng origin của SPA. Giới hạn body 8 KB cho route auth.

## Token trong URL

Liên kết xác thực email và đặt lại mật khẩu đặt token trong **fragment** (`#token=...`), không dùng query string — tránh lọt vào log máy chủ và header `Referer`. Frontend xóa token khỏi thanh địa chỉ bằng `history.replaceState` ngay sau khi dùng.

## Tài liệu tải lên và dữ liệu

- Quét tệp tải lên (mức 2, #11); audit log cho sự kiện xác thực; che dữ liệu nhạy cảm (PII masking) khi cần.
- Chính sách dữ liệu gửi ra LLM bên ngoài cần rà soát; xóa toàn bộ dữ liệu khi người dùng yêu cầu (mức 2).
- Lưu `terms_accepted_at` + `terms_version`; xóa tài khoản đáp ứng quyền xóa dữ liệu — rà soát pháp lý theo Nghị định 13/2023/NĐ-CP trước khi mở công khai.

## Email gửi ra

Cấu hình **SPF, DKIM, DMARC**, dùng subdomain riêng (`mail.example.com`). Liên kết luôn HTTPS, có bản văn bản thuần. Job gửi email retry cấp số nhân, **không ghi nội dung token vào log**.

## Kiểm thử bảo mật

Quét SAST (`gosec`, `govulncheck`) + quét phụ thuộc trong CI. Kiểm tra thủ công: XSS không đọc được access token từ lưu trữ trình duyệt; cookie đủ thuộc tính; không có token trong log và URL gửi đi. Kiểm thử tải đăng nhập với argon2id để chọn tham số.
