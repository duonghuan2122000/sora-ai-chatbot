---
title: Thay đổi từ v1 sang v2
date: 2026-10-04
tags: [quyet-dinh, lich-su]
sources: [docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md, docs/ai-chatbot-tong-quan.md]
---

# Thay đổi từ v1 sang v2

Bản v1 ([[tong-quan-v1]]) là *bản nháp* cho hệ thống **nội bộ**; v2 ([[nghiep-vu-uu-tien-v2]]) chuyển thành **sản phẩm công khai kiểu ChatGPT** và là nguồn sự thật hiện hành.

| Nội dung | v1 | v2 |
|---|---|---|
| Bối cảnh | Hệ thống nội bộ | Sản phẩm công khai kiểu ChatGPT |
| Phân quyền | `document_acl` theo nhóm/vai trò | **Cô lập theo `user_id`**, bắt buộc từ MVP ([[co-lap-du-lieu-theo-user-id]]) |
| Xác thực | Keycloak (OIDC) hoặc hệ thống sẵn có | **Tự build**, lên mức 1 với phạm vi lớn hơn ([[m1-tai-khoan-va-xac-thuc]]) |
| Rate limit, quota | Mức 2 | **Mức 1** — bảo vệ chi phí LLM |
| Chế độ chat | Chỉ hỏi đáp theo tài liệu | **Chat thông thường + chat theo tài liệu** |
| Cấu hình người dùng | Không có | **Module M3**, mức 1 ([[modules-m1-m8]]) |
| Widget nhúng | Mức 4 (mở rộng) | **Bỏ hoặc hoãn** |
| Qdrant | Hạng mục mở rộng | **Quyết định cần chốt ở MVP** ([[vector-search-mariadb]]) |
| Tự host LLM | Đề xuất (Qwen/Llama qua vLLM/Ollama) | Đã chốt **dùng API** |

## Hệ quả khi đọc tài liệu

Khi một claim trong v1 xung đột với v2 → **v2 thắng**. Các phần v1 còn giá trị: kiến trúc RAG, luồng ingestion/query, danh sách rủi ro kỹ thuật của MariaDB, gợi ý công nghệ chưa chốt (embedding, reranker, OCR, giám sát). Ghi chú chi tiết ở [[tong-quan-v1]].
