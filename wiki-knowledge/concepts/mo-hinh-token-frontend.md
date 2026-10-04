---
title: Mô hình token phía frontend
date: 2026-10-04
tags: [frontend, m1, token, de-lam-sai]
sources: [docs/auth/m1-tai-khoan-va-xac-thuc.md]
---

# Mô hình token phía frontend

Phần **dễ làm sai nhất** của M1. Tóm tắt: access token trong bộ nhớ, refresh token trong cookie, và app phải chờ refresh xong mới chạy router guard.

## Pinia store `useAuthStore`

- `accessToken` — **chỉ trong bộ nhớ**, KHÔNG `localStorage` (giảm rủi ro XSS đánh cắp).
- `user`, `status` (`unknown` | `anonymous` | `authenticated`).

## Bốn điểm dễ sai

1. **Khởi động app:** reload trang là mất access token → gọi `POST /auth/refresh` lúc khởi động. Thành công → `authenticated`; 401 → `anonymous`. **Router phải chờ** bước này xong trước khi chạy guard — nếu không sẽ nhấp nháy về trang đăng nhập.
2. **HTTP client:** khi nhận 401, chạy **một** lần refresh **dùng chung (single-flight)** rồi thử lại request; thất bại thì đăng xuất cục bộ + chuyển `/dang-nhap`. Nếu mỗi request tự refresh thì một loạt 401 sẽ tạo một loạt refresh và kích hoạt reuse detection ([[xac-thuc-va-token]]).
3. **Nhiều tab:** bọc lời gọi refresh trong `navigator.locks.request('auth-refresh', …)` để các tab không làm mới đồng thời; đồng bộ đăng xuất giữa các tab bằng `BroadcastChannel`.
4. **SSE của chat:** `EventSource` **không gửi được header** `Authorization` → dùng `fetch` + `ReadableStream` (hoặc `@microsoft/fetch-event-source`).

Thêm: đặt hẹn giờ làm mới **chủ động** khi token còn khoảng 60 giây.

## Tuyến đường và guard

| Đường dẫn | Guard |
|---|---|
| `/dang-nhap`, `/dang-ky`, `/quen-mat-khau` | chỉ khách |
| `/kiem-tra-hop-thu` | khách hoặc đã đăng nhập |
| `/xac-thuc-email`, `/dat-lai-mat-khau` (`#token=`) | công khai |
| `/cai-dat/bao-mat`, `/cai-dat/phien-dang-nhap`, `/cai-dat/du-lieu` | cần đăng nhập |

## Biểu mẫu

`vee-validate` + `zod` kiểm tra phía client; **lỗi từng trường** chỉ cho lỗi định dạng, **lỗi đăng nhập hiển thị banner chung** (không lộ trường nào sai — [[chong-do-email]]). `autocomplete` đúng: `current-password` (đăng nhập), `new-password` (đăng ký/đặt lại/đổi); không chặn dán. Thành phần thêm: `PasswordInput` (hiện/ẩn), `PasswordStrength` (bọc `zxcvbn`, **nạp động** để không tăng bundle ban đầu).

Trên di động: `h-dvh`, cỡ chữ nhập liệu **16px** để iOS không tự phóng to.
