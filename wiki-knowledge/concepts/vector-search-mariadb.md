---
title: Vector search trên MariaDB — rủi ro và phương án
date: 2026-10-04
tags: [rag, rui-ro, mariadb, qdrant]
sources: [docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md, docs/ai-chatbot-tong-quan.md]
---

# Vector search trên MariaDB

**Rủi ro lớn nhất phải kiểm chứng ở MVP** (v2 §5.1). Không phải hạng mục mở rộng — là *quyết định cần chốt sớm*.

## Vấn đề

MariaDB lọc kết hợp với vector search yếu hơn pgvector: index vector trả top-K gần nhất **toàn cục**, rồi mới lọc. Với kho tài liệu riêng từng người dùng, top 50–100 vector gần nhất phần lớn thuộc người khác → sau khi lọc theo `user_id` có thể còn rất ít hoặc **không còn kết quả**.

## Ba phương án

| Phương án | Khi nào hợp lý |
|---|---|
| Quét trực tiếp theo `user_id`, không dùng index vector | Mỗi người dùng có ít chunk |
| Giữ MariaDB, lấy dư ứng viên lớn, theo dõi tỷ lệ bị loại | Quy mô nhỏ, chấp nhận độ chính xác thấp hơn |
| Chuyển sang Qdrant (lọc theo payload) | Nhiều người dùng, nhiều chunk |

Quyết định phụ thuộc quy mô dự kiến — hiện **chưa có số** ([[diem-con-mo]]).

## Ràng buộc kỹ thuật của MariaDB (từ v1)

- Một index vector mỗi bảng; cột vector phải `NOT NULL`.
- RAM của index vector khi dữ liệu lớn → đặt `mhnsw_max_cache_size`, theo dõi và đo thực tế.
- FULLTEXT tiếng Việt kém (tách từ theo khoảng trắng) → tiền xử lý tách từ vào cột riêng, hợp nhất bằng RRF trong Go.
- JSON của MariaDB không phải JSONB → tách trường phân quyền ra bảng quan hệ, dùng generated column có index cho trường cần lọc nhanh.

## Cách ly rủi ro

Bọc truy cập sau interface **`VectorStore`** ([[kien-truc-he-thong]]), làm thử nghiệm tải ở giai đoạn MVP, và **theo dõi tỷ lệ ứng viên bị loại** khi lọc theo `user_id` — chỉ số này là tín hiệu để quyết định tách Qdrant.

Embedding gợi ý BGE-M3 (vector 1024 chiều) hoặc multilingual-e5 — [[ngan-xep-ky-thuat]].
