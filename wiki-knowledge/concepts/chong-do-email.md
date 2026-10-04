---
title: Chống dò email
date: 2026-10-04
tags: [m1, bao-mat, privacy]
sources: [docs/auth/m1-tai-khoan-va-xac-thuc.md, docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md]
---

# Chống dò email

Mục tiêu chất lượng #1 của M1: **không lộ việc một email đã có tài khoản hay chưa**. Đăng ký công khai rất dễ bị quét hàng loạt email.

## Ba endpoint luôn trả 202 cùng nội dung

`POST /auth/register` · `POST /auth/forgot-password` · `POST /auth/verify-email/resend`

Luôn **202** với **cùng nội dung**, bất kể email tồn tại hay không. Thông tin thật đi qua email:

- Email chưa tồn tại → gửi link xác thực.
- Email **đã** tồn tại → gửi email "tài khoản đã tồn tại" kèm liên kết đăng nhập và quên mật khẩu (đến đúng chủ hộp thư).
- Quên mật khẩu → "Nếu email này có tài khoản, liên kết đặt lại mật khẩu sẽ đến trong ít phút."

Việc gửi email đẩy vào **Asynq** (không đồng bộ) để **độ trễ hai nhánh gần nhau** — chống cả dò bằng thời gian, xem [[mat-khau-va-chong-lam-dung]].

## Đăng nhập

Thông báo duy nhất **"Email hoặc mật khẩu không đúng."** Khi không tìm thấy user, **vẫn chạy `Verify`** với một `dummyHash` (sinh lúc khởi động với cùng tham số `DefaultParams`) để thời gian phản hồi không phân biệt được hai trường hợp.

## Nội dung thông báo đã chốt

| Tình huống | Nội dung |
|---|---|
| Sau đăng ký | "Nếu địa chỉ này dùng được, chúng tôi đã gửi liên kết xác thực. Liên kết có hiệu lực trong 24 giờ." |
| Quên mật khẩu | "Nếu email này có tài khoản, liên kết đặt lại mật khẩu sẽ đến trong ít phút. Liên kết hết hạn sau 30 phút." |
| Đăng nhập sai | "Email hoặc mật khẩu không đúng." |

Nguyên tắc chung của [[quy-tac-thiet-ke]]: lỗi nói rõ đã xảy ra gì và cách khắc phục, nhưng **không tiết lộ thông tin giúp kẻ tấn công**. Ô nhập lỗi cũng không nêu gợi ý.

## Kiểm thử

Nhóm ca kiểm thử riêng trong [[m1-tai-khoan-va-xac-thuc]] §12.1: email trùng vẫn 202 và gửi email "đã tồn tại"; user không tồn tại cho cùng phản hồi **và thời gian gần nhau**; đếm sai theo email kể cả email không tồn tại.
