---
title: Xác thực và token
date: 2026-10-04
tags: [m1, bao-mat, jwt, token]
sources: [docs/auth/m1-tai-khoan-va-xac-thuc.md]
---

# Xác thực và token

## Cặp token

| | Access token | Refresh token |
|---|---|---|
| Dạng | **JWT ký EdDSA (Ed25519)** | Chuỗi ngẫu nhiên **256 bit** (32 byte `crypto/rand`, base64url) — *không phải JWT* |
| Sống | **15 phút** | Trượt **30 ngày**, tối đa tuyệt đối **90 ngày** |
| Lưu ở client | **Bộ nhớ** (Pinia), không localStorage | Cookie `HttpOnly` |
| Lưu ở server | Không (stateless) | **SHA-256** trong `sessions.refresh_hash` |

Claim: `sub` (user id), `sid`, `iat`, `exp`, `iss`, `aud`. **Không** chứa email hay vai trò nhạy cảm. Khóa ký trong Secret, có `kid` để xoay khóa.

## Xoay vòng và phát hiện đánh cắp

Mỗi lần refresh: sinh token mới, chuyển token hiện tại thành `prev_refresh_hash`, ghi `rotated_at`. Dùng lại token **cũ** ngoài **cửa sổ ân hạn 10 giây** (ân hạn để chịu nhiều tab làm mới cùng lúc) → **thu hồi cả phiên** với lý do `reuse_detected` + ghi audit. Đây là chuẩn *refresh token rotation with reuse detection*.

Ghi có khóa lạc quan: `UPDATE … WHERE id=? AND refresh_hash=?` — 0 dòng bị ảnh hưởng (đua giữa hai tab) → coi như phiên hết hạn.

## Thu hồi tức thời

Access token là stateless nên không thu hồi được bằng DB. Giải pháp: khi thu hồi phiên, ghi `sid:revoked:{sid}` vào Redis với **TTL 15 phút** (bằng đời access token). `RequireAuth` kiểm tra Redis sau khi verify chữ ký. Nhờ vậy đăng xuất/khóa có hiệu lực **ngay**, không phải chờ token hết hạn.

Sự kiện thu hồi: đăng xuất, đăng xuất mọi thiết bị, đổi mật khẩu (thu hồi các phiên khác, giữ phiên hiện tại theo `sign_out_others`), đặt lại mật khẩu (thu hồi **tất cả**), admin khóa, xóa tài khoản, reuse detected.

## Cookie

```
Set-Cookie: __Secure-rt=<token>; Path=/api/v1/auth; HttpOnly; Secure; SameSite=Strict; Max-Age=2592000
```

`Path` giới hạn để cookie chỉ gửi kèm route `/auth/*` — chỉ `/auth/refresh` và `/auth/logout` dùng cookie; mọi route khác dùng `Authorization: Bearer` (nên không bị CSRF). Chi tiết header/CSRF ở [[bao-mat-ung-dung]].

## Middleware

`RequireAuth` — verify chữ ký, hạn, `iss`, `aud` → tra `sid:revoked` → `c.Set("uid", …)`, `c.Set("sid", …)`.
`RequireVerified` — chặn route chat và tài liệu bằng 403 `email_not_verified` nếu `email_verified_at` NULL (cache ngắn trong Redis hoặc đưa cờ `ver` vào claim, chấp nhận trễ tối đa 15 phút).

## Phía client

Reload trang mất access token → app gọi `/auth/refresh` khi khởi động. Chi tiết ở [[mo-hinh-token-frontend]].

## Liên quan

Token email (xác thực, đặt lại mật khẩu) là loại khác: một lần dùng, lưu SHA-256, đặt trong **fragment** URL — xem [[m1-tai-khoan-va-xac-thuc]] và [[bao-mat-ung-dung]].
