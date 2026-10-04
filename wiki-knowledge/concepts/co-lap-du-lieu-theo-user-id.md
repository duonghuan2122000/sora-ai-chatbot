---
title: Cô lập dữ liệu theo user_id
date: 2026-10-04
tags: [bao-mat, m6, quy-tac-bat-di-bat-dich]
sources: [docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md, docs/auth/m1-tai-khoan-va-xac-thuc.md]
---

# Cô lập dữ liệu theo `user_id`

**Ưu tiên #7 (mức 1)** — hạng mục duy nhất trong bảng ưu tiên được in đậm, và **có kiểm thử riêng**, vì lỗi ở đây là **lộ tài liệu giữa người dùng**.

## Quy tắc

1. Lấy `uid` từ **context của middleware** (JWT claim `sub`), **không bao giờ** nhận `user_id` từ client.
2. **Mọi** truy vấn tài liệu / chunk / hội thoại đều lọc theo `uid`.
3. Kiểm soát ở **backend** khi truy xuất — **không dựa vào prompt**.

```go
c.Set("uid", claims.Subject)   // trong RequireAuth
// mọi repo call sau đó nhận uid từ context
```

## Vì sao không dựa vào prompt

Phạm vi tài liệu là quyết định của backend theo `uid`, **không phụ thuộc nội dung prompt** — kể cả prompt cấu hình do người dùng viết (M3). Prompt người dùng là dữ liệu không tin cậy; nếu phạm vi phụ thuộc prompt thì prompt injection trở thành lỗ hổng đọc chéo dữ liệu. Xem [[bao-mat-ung-dung]].

## Thay thế gì

Bản v1 dùng `document_acl` phân quyền theo nhóm/vai trò — **đã bỏ**, thay bằng cô lập theo chủ sở hữu ([[thay-doi-v1-sang-v2]]). Hệ quả: không có chia sẻ tài liệu giữa người dùng ở mô hình hiện tại.

## Liên quan

- Lỗ hổng tương tự ở tầng API: thu hồi/xem phiên đăng nhập của người khác (IDOR) — có ca kiểm thử riêng trong [[m1-tai-khoan-va-xac-thuc]] §12.1.
- Xóa tài khoản theo yêu cầu phải dọn chunk + vector + hội thoại của đúng `uid` — [[thiet-ke-du-lieu-mariadb]].
