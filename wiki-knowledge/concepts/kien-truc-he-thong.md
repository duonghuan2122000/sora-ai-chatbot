---
title: Kiến trúc hệ thống
date: 2026-10-04
tags: [kien-truc]
sources: [docs/ai-chatbot-tong-quan.md, docs/auth/m1-tai-khoan-va-xac-thuc.md]
---

# Kiến trúc hệ thống

```
Vue SPA ──HTTPS──► Go/Gin API ──┬──► MariaDB   (users, sessions, auth_tokens, documents, chunks, conversations…)
  │  access token (bộ nhớ)      ├──► Redis     (rate limit, đếm sai, sid thu hồi, cooldown email, cache)
  │  refresh cookie (HttpOnly)  ├──► MinIO     (file tài liệu gốc)
  │                             ├──► Asynq ──► Worker ──► nhà cung cấp email
  └──► Cloudflare Turnstile     ├──► dịch vụ embedding / reranker
                                └──► LLM (qua API, bọc sau lớp trừu tượng)
```

Hai luồng chạy nền: **ingestion** (tải tệp → MinIO → trích xuất → chunk → embedding → MariaDB) và **hàng đợi email** (Asynq, để phản hồi không phụ thuộc việc gửi thư). Chi tiết ở [[rag]].

## Quyết định kiến trúc đáng nhớ

- **SPA và API nên cùng site** (`app.example.com` + `api.example.com`, hoặc chung domain qua reverse proxy `/api`) để cookie `SameSite=Strict` chạy được và không cần CORS rộng. Còn là điểm mở — [[diem-con-mo]].
- **LLM bọc sau lớp trừu tượng nhà cung cấp** (ưu tiên #3) để đổi nhà cung cấp và đo chi phí theo token mỗi lượt.
- **Vector bọc sau interface `VectorStore`** để giữ hay tách MariaDB/Qdrant chỉ là đổi implementation — [[vector-search-mariadb]].
- **Asynq cho mọi việc gửi email** — vừa để không chặn request, vừa để hai nhánh có/không có email có độ trễ gần nhau ([[chong-do-email]]).
- **Redis giữ `sid` đã thu hồi** thay vì tra DB mỗi request — [[xac-thuc-va-token]].

## Trạng thái code

Xem [[ngan-xep-ky-thuat]] — `app/backend/` trống hoàn toàn, `app/frontend/` còn là scaffold `npm create vue@latest`.
