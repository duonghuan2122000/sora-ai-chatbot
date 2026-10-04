---
title: Mật khẩu và chống lạm dụng
date: 2026-10-04
tags: [m1, bao-mat, argon2id, rate-limit]
sources: [docs/auth/m1-tai-khoan-va-xac-thuc.md]
---

# Mật khẩu và chống lạm dụng

## Băm mật khẩu — argon2id

| Mục | Giá trị |
|---|---|
| Tham số khởi điểm | `m=64 MiB, t=3, p=2`, salt 16 byte, đầu ra 32 byte |
| Lưu | Chuỗi **PHC** (`$argon2id$v=19$m=…,t=…,p=…$salt$hash`) — tham số nằm trong chuỗi nên nâng cấp được |
| Hiệu chỉnh | Đo trên máy chủ thật để một lần băm mất **250–500 ms** |
| Rehash | Sau đăng nhập đúng, nếu tham số lưu cũ hơn hiện hành → băm lại |
| Giới hạn đồng thời | Semaphore ≈ số lõi CPU — tránh cạn RAM khi bị tấn công |
| Pepper (tùy chọn) | HMAC-SHA256 với khóa trong Secret trước khi băm |

**Chính sách mật khẩu:** 10–128 ký tự, cho phép mọi ký tự Unicode, **không** bắt ký tự đặc biệt, không cắt khoảng trắng (theo NIST SP 800-63B). Kiểm tra danh sách bị lộ qua API *Pwned Passwords* theo k-anonymity (chỉ gửi 5 ký tự đầu của SHA-1) — **nếu dịch vụ lỗi thì bỏ qua kiểm tra**, không chặn người dùng. Thêm danh sách chặn cục bộ: mật khẩu phổ biến, chứa email hoặc tên hiển thị. `zxcvbn` ở frontend chỉ để gợi ý độ mạnh; backend vẫn kiểm tra.

## Giới hạn thử sai và rate limit

| Hành động | Ngưỡng | Phản ứng |
|---|---|---|
| Đăng nhập sai / email | 3 lần / 15 phút | Bắt buộc CAPTCHA |
| Đăng nhập sai / email | 5 lần / 15 phút | Khóa tạm 15 phút, 429 kèm `retry_after` |
| Đăng nhập / IP | 20 lần / 15 phút | 429 |
| Đăng ký / IP | 5 lần / giờ | 429 |
| Quên mật khẩu | 3/giờ/email, 10/giờ/IP | 202 (không gửi thêm) hoặc 429 |
| Gửi lại email xác thực | 1/60 giây, 5/giờ/user | 429 `cooldown` |
| Đổi mật khẩu | 5/giờ/user | 429 |
| Làm mới phiên | 60/phút/phiên | 429 |

**Bộ đếm theo email đã chuẩn hóa dù tài khoản có tồn tại hay không** → hành vi khóa giống hệt nhau, không lộ thông tin ([[chong-do-email]]). Không khóa vĩnh viễn, để tránh bị lợi dụng khóa tài khoản người khác.

IP thật lấy từ header của reverse proxy **tin cậy** (cấu hình `TrustedProxies` của Gin) — không tin `X-Forwarded-For` tùy tiện.

CAPTCHA: **Cloudflare Turnstile** (hoặc hCaptcha) — nạp khi đăng ký, quên mật khẩu, và đăng nhập sau 3 lần sai. Còn là điểm mở: [[diem-con-mo]].

## Ghi chú cho wiki

Cấu hình `registration_mode` (`open` | `invite`) nằm trong cấu hình hệ thống để đổi chính sách đăng ký không phải sửa code — nhưng chính sách chọn gì thì chưa chốt, và nó quyết định giai đoạn B/C của M1 là bắt buộc hay không.
