---
title: Tổng quan hệ thống (v1)
date: 2026-10-04
tags: [source, kien-truc, v1]
sources: [docs/ai-chatbot-tong-quan.md]
---

# Tổng quan hệ thống — bản nháp v1

> ⚠️ **Đã bị thay một phần.** Bản v1 giả định hệ thống **nội bộ** với phân quyền `document_acl` theo nhóm, Keycloak, widget nhúng. [[nghiep-vu-uu-tien-v2]] đã bỏ cả ba. Đọc trang này như *bối cảnh kỹ thuật*, không phải quyết định hiện hành — xem [[thay-doi-v1-sang-v2]].

## Còn giá trị

- **Kiến trúc RAG và luồng dữ liệu** — vẫn đúng, xem [[rag]] và [[kien-truc-he-thong]].
- **Rủi ro kỹ thuật của MariaDB**: một index vector mỗi bảng, cột vector phải `NOT NULL`, lọc kết hợp yếu hơn pgvector, FULLTEXT tiếng Việt tách từ theo khoảng trắng, RAM của index vector (`mhnsw_max_cache_size`), JSON không phải JSONB. Xem [[vector-search-mariadb]].
- **Gợi ý công nghệ chưa chốt**: embedding BGE-M3 (vector 1024 chiều) hoặc multilingual-e5, reranker `bge-reranker-v2-m3`, xử lý tài liệu Docling/Apache Tika, PaddleOCR cho bản scan, giám sát Langfuse + Prometheus/Grafana + OpenTelemetry.
- **Ghi chú frontend**: shadcn-vue/Reka UI là headless nên responsive do Tailwind; `h-dvh` cho khung chat mobile; chưa có DataTable dựng sẵn, cân nhắc TanStack Table cho bảng quản trị.
- **Luồng hỏi đáp đầy đủ** (lấy dư top 50–100 vector + FULLTEXT → RRF → lọc quyền → rerank → ghép ngữ cảnh → stream).

## Đã hết hiệu lực

| v1 | Nay |
|---|---|
| `document_acl` phân quyền theo nhóm | Cô lập theo `user_id` — [[co-lap-du-lieu-theo-user-id]] |
| Keycloak (OIDC) + JWT | Tự build — [[m1-tai-khoan-va-xac-thuc]] |
| Widget nhúng, thiết kế theo `@container` | Bỏ/hoãn |
| Chỉ có chế độ hỏi đáp theo tài liệu | Thêm chat thông thường |
| Qdrant là "mở rộng" | Là quyết định cần chốt ở MVP |
