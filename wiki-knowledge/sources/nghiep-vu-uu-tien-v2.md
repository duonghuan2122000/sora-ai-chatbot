---
title: Nghiệp vụ và thứ tự ưu tiên v2
date: 2026-10-04
tags: [source, nghiep-vu, uu-tien]
sources: [docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md]
---

# Nghiệp vụ và thứ tự ưu tiên (v2)

**Đây là nguồn sự thật của dự án.** Khi mâu thuẫn với bản v1 ([[tong-quan-v1]]) thì file này thắng.

## Định hướng sản phẩm

Website chatbot công khai, trải nghiệm kiểu ChatGPT/DeepSeek, hai chế độ chat:

- **Chat thông thường** — hỏi đáp trực tiếp với LLM, không cần tài liệu.
- **Chat theo tài liệu (RAG)** — trả lời dựa trên kho tài liệu riêng của chính người dùng, kèm trích dẫn nguồn.

Mỗi người dùng có **cấu hình mặc định** riêng: prompt hướng dẫn, mô hình, phạm vi tài liệu ([[modules-m1-m8]] M3).

## Quyết định đã chốt

| Chủ đề | Quyết định |
|---|---|
| LLM | Dùng qua API (chưa chốt nhà cung cấp) |
| Đối tượng | Người dùng công khai trên website |
| Tài liệu | Kho riêng theo từng người dùng |
| Xác thực | **Tự build**, không dùng Keycloak |
| Nền | Go (Gin), MariaDB có `VECTOR`, Vue 3 + shadcn-vue + Tailwind |

## Thứ tự ưu tiên

- **Mức 1 (MVP):** #1 đăng ký/đăng nhập/JWT + refresh + đổi mật khẩu · #2 chat thường (streaming, Markdown, danh sách hội thoại) · #3 lớp trừu tượng LLM + ghi token · #4 cấu hình mặc định · #5 kho tài liệu cá nhân · #6 chat theo tài liệu có trích dẫn · **#7 cô lập dữ liệu theo `user_id`** · #8 rate limit + quota · #9 admin tối thiểu.
- **Mức 2:** #10 xác thực email + quên mật khẩu · #11 chống injection, quét tệp, audit log, chính sách dữ liệu ra LLM · #12 hybrid search + reranker · #13 feedback 👍/👎 · #14 giám sát chi phí · #15 phạm vi tài liệu theo nhóm.
- **Mức 3:** #16 bộ câu hỏi đánh giá RAG · #17 gói dịch vụ · #18 đăng nhập Google/GitHub · #19 thống kê feedback · #20 quản lý phiên bản tài liệu.

Chi tiết từng module: [[modules-m1-m8]].

## Rủi ro phải kiểm chứng sớm

1. **Vector search theo từng người dùng trên MariaDB** — xem [[vector-search-mariadb]]. Việc giữ MariaDB hay tách Qdrant là *quyết định cần chốt ở MVP*, không còn là hạng mục mở rộng.
2. **Prompt cấu hình của người dùng** là dữ liệu không tin cậy — xem [[bao-mat-ung-dung]].
3. **Chi phí LLM** tăng theo mức sử dụng → quota theo người dùng, ghi token từng lượt, cảnh báo ngưỡng.

## Đã bỏ hoặc hoãn

Widget nhúng · tự host LLM · Keycloak · `document_acl` theo nhóm/vai trò (thay bằng cô lập theo chủ sở hữu).

## Ghi chú cho wiki

- Mốc `M1`–`M8` là trục phân loại chính của wiki này → [[modules-m1-m8]].
- Ưu tiên #7 sinh ra quy tắc bất di bất dịch trong [[co-lap-du-lieu-theo-user-id]].
- Các điểm chưa chốt ở §8 đã gộp vào [[diem-con-mo]].
