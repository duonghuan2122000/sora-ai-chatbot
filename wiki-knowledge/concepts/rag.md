---
title: RAG — nạp tài liệu và hỏi đáp có trích dẫn
date: 2026-10-04
tags: [rag, m4, m5]
sources: [docs/ai-chatbot-tong-quan.md, docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md]
---

# RAG — luồng nạp tài liệu và hỏi đáp

Sản phẩm khác ChatGPT ở chỗ câu trả lời **có dẫn nguồn từ tài liệu của chính người dùng** — đây là nguyên tắc thiết kế #1 của [[quy-tac-thiet-ke]] và là lý do tồn tại của module M4/M5 ([[modules-m1-m8]]).

## Luồng nạp tài liệu (ingestion, M4)

Tải tệp lên → lưu MinIO → trích xuất văn bản (OCR nếu là bản scan) → chia chunk theo ngữ nghĩa kèm metadata (nguồn, trang) → tạo embedding → ghi `chunks` vào MariaDB. Chạy trong worker nền (Asynq), trạng thái nạp hiển thị theo tài liệu: Đang xử lý / Sẵn sàng / Lỗi.

Giới hạn dung lượng và số tài liệu mỗi người; xóa tài liệu phải xóa kèm chunk và vector.

## Luồng hỏi đáp (M5)

Câu hỏi → embedding → **tìm vector (lấy dư top 50–100)** + FULLTEXT → hợp nhất bằng **RRF** → **lọc theo quyền truy cập** → rerank → ghép ngữ cảnh vào prompt → LLM trả lời dạng stream kèm trích dẫn → lưu hội thoại + feedback 👍/👎.

- Hybrid search (vector + FULLTEXT + RRF) và reranker là **mức 2 (#12)**, nhưng quan trọng với tiếng Việt vì FULLTEXT của MariaDB tách từ theo khoảng trắng.
- Trả lời **"không tìm thấy trong tài liệu của bạn"** kèm gợi ý khi thiếu thông tin — không hiển thị chip trích dẫn. Xem [[quy-tac-thiet-ke]] §8.2.
- Phạm vi tài liệu do **backend quyết theo `uid`**, không phụ thuộc nội dung prompt — [[co-lap-du-lieu-theo-user-id]] và [[bao-mat-ung-dung]].

## Hai chế độ chat

**Chat thông thường** (không tài liệu) và **Theo tài liệu** (RAG). Người dùng chọn khi bắt đầu hoặc đổi trong hội thoại; chế độ hiện tại luôn nhìn thấy trong ô soạn tin (chip, [[quy-tac-thiet-ke]] §8.4).

## Rủi ro

Vector search theo từng người dùng trên MariaDB có thể hết kết quả sau khi lọc `user_id` — [[vector-search-mariadb]]. Nội dung tài liệu cũng là **nguồn prompt injection**, xem [[bao-mat-ung-dung]].
