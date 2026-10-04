---
title: Tám module nghiệp vụ M1–M8
date: 2026-10-04
tags: [nghiep-vu, module]
sources: [docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md]
---

# Tám module nghiệp vụ M1–M8

`M1`–`M8` là **trục phân loại chính** của dự án (ưu tiên, màn hình, wiki này). Số `#n` là thứ tự trong bảng ưu tiên của [[nghiep-vu-uu-tien-v2]].

| # | Module | Nội dung | Ưu tiên |
|:---:|---|---|---|
| M1 | Tài khoản và xác thực | Đăng ký, đăng nhập/ra, đổi/quên mật khẩu, JWT + refresh, xác thực email | #1, #10 ([[m1-tai-khoan-va-xac-thuc]]) |
| M2 | Chat | Chat thường + theo tài liệu, streaming SSE, Markdown, dừng/tạo lại, danh sách hội thoại; lớp trừu tượng LLM + ghi token | #2, #3 ([[rag]]) |
| M3 | Cấu hình mặc định | Prompt riêng, mô hình mặc định, phạm vi tài liệu; ghi đè theo hội thoại; giới hạn độ dài, không ghi đè quy tắc hệ thống | #4, #15 ([[bao-mat-ung-dung]]) |
| M4 | Kho tài liệu cá nhân | Tải lên, trích xuất, OCR, chunk, embedding, chỉ mục; theo dõi trạng thái nạp; nhóm/xóa; giới hạn dung lượng | #5, #15, #20 ([[rag]]) |
| M5 | Truy xuất và trả lời có trích dẫn | Tìm trong tài liệu của chính người hỏi; hybrid search + reranker (sau); trích dẫn; trả lời "không biết" | #6, #12 ([[rag]], [[vector-search-mariadb]]) |
| M6 | Cô lập dữ liệu và bảo mật | Cô lập theo `user_id`; rate limit, quota; chống prompt injection; quét tệp; audit log; xóa dữ liệu theo yêu cầu | **#7**, #8, #11 ([[co-lap-du-lieu-theo-user-id]]) |
| M7 | Quản trị | Xem/khóa người dùng, xem mức sử dụng và chi phí; sau: thống kê feedback, cấu hình qua UI | #9, #17, #19 |
| M8 | Phản hồi, giám sát, đánh giá | 👍/👎, nhật ký hội thoại; Prometheus/Grafana + Langfuse; bộ câu hỏi kiểm thử RAG | #13, #14, #16 |

## Màn hình cần thiết kế theo module

Từ [[quy-tac-thiet-ke]] §9: M1 **đã xong 14 màn hình** ([[man-hinh-m1]]); M2 cần khung chat, danh sách hội thoại, tìm kiếm, rỗng, lỗi gửi/hết quota; M3 cấu hình hai cột; M4 danh sách + tải lên + trạng thái nạp; M5 nằm trong khung chat; M6 mức sử dụng/quota; M7 danh sách người dùng + chi phí; M8 nhật ký + thống kê feedback.

## Đọc theo module

- Nhóm A – nền tảng hạ tầng dùng chung: [[kien-truc-he-thong]], [[ngan-xep-ky-thuat]], [[thiet-ke-du-lieu-mariadb]]
- Nhóm B – bảo mật xuyên suốt M1/M6: [[xac-thuc-va-token]], [[mat-khau-va-chong-lam-dung]], [[chong-do-email]], [[bao-mat-ung-dung]], [[mo-hinh-token-frontend]]
- Nhóm C – RAG (M2/M4/M5): [[rag]], [[vector-search-mariadb]]
