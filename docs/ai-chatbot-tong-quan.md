# Hệ thống AI Chatbot hỗ trợ thông tin — Tóm tắt sơ bộ

> Trạng thái: bản nháp ban đầu. Mục **Đã chốt** là quyết định của dự án; mục **Đề xuất** là gợi ý, cần xác nhận trước khi triển khai.

## 1. Mục tiêu

Xây dựng chatbot trả lời câu hỏi dựa trên tài liệu và dữ liệu nội bộ, theo kiến trúc **RAG** (Retrieval-Augmented Generation): truy xuất đoạn tài liệu liên quan rồi để LLM tổng hợp câu trả lời, kèm trích dẫn nguồn.

## 2. Công nghệ

### Đã chốt

| Lớp | Công nghệ |
|---|---|
| Backend | Go, framework **Gin** |
| Cơ sở dữ liệu | **MariaDB** (bản có kiểu `VECTOR`, khuyến nghị 11.8 LTS trở lên) |
| Frontend | **Vue 3**, **shadcn-vue** (Reka UI) + **Tailwind CSS** |

### Đề xuất (chưa chốt)

| Lớp | Gợi ý | Ghi chú |
|---|---|---|
| LLM | Qua API (Claude, GPT, Gemini) hoặc tự host (Qwen, Llama qua vLLM/Ollama) | Quyết định theo yêu cầu về dữ liệu không được ra ngoài |
| Embedding | BGE-M3 hoặc multilingual-e5 | Cần hỗ trợ tốt tiếng Việt; BGE-M3 cho vector 1024 chiều |
| Reranker | bge-reranker-v2-m3 | Bù cho chất lượng truy xuất của MariaDB |
| Xử lý tài liệu | Docling / Apache Tika, PaddleOCR cho tài liệu scan | Thường chạy thành microservice Python nhỏ |
| Cache, hàng đợi | Redis, Asynq | Cache, rate limit, tác vụ nền (ingestion) |
| Lưu file gốc | MinIO (tương thích S3) | |
| Xác thực | Keycloak (OIDC) + JWT | |
| Giám sát | Langfuse, Prometheus + Grafana, OpenTelemetry | |
| Triển khai | Docker, Kubernetes hoặc Docker Compose | |

## 3. Kiến trúc tổng quan

```
Vue (shadcn-vue + Tailwind)
   │  HTTPS, SSE (stream câu trả lời)
   ▼
Go / Gin API ──► Redis (cache, rate limit, hàng đợi)
   │
   ├──► MariaDB (người dùng, hội thoại, chunk, vector, ACL)
   ├──► MinIO (file tài liệu gốc)
   ├──► Dịch vụ embedding / reranker
   └──► LLM (API hoặc tự host)

Worker nền (Go) ──► Dịch vụ xử lý tài liệu (Python) ──► MariaDB, MinIO
```

## 4. Luồng chính

**Nạp tài liệu (ingestion):** tải file lên → lưu MinIO → trích xuất văn bản → chia chunk theo ngữ nghĩa (kèm metadata: nguồn, trang) → tạo embedding → ghi vào MariaDB.

**Hỏi đáp:** nhận câu hỏi → tạo embedding → tìm vector (lấy dư top 50–100) kết hợp FULLTEXT → hợp nhất bằng RRF → lọc theo quyền truy cập → rerank → ghép ngữ cảnh vào prompt → LLM trả lời dạng stream, kèm trích dẫn → lưu hội thoại và phản hồi 👍/👎.

## 5. Thiết kế dữ liệu (MariaDB)

- `documents`, `chunks` (cột `embedding VECTOR(1024)`, `VECTOR INDEX`, `FULLTEXT`), `document_acl` (phân quyền dạng bảng quan hệ), cùng các bảng người dùng, hội thoại, tin nhắn, phản hồi.
- Metadata linh hoạt lưu `JSON`; trường cần lọc nhanh thì tạo generated column có index.
- Dùng `utf8mb4` để lưu tiếng Việt đầy đủ.

## 6. Frontend

- **Trang quản trị:** quản lý tài liệu, người dùng, nhật ký hội thoại, feedback.
- **Giao diện chat:** streaming, hiển thị Markdown, trích dẫn nguồn, nút phản hồi.
- **Widget nhúng:** đóng gói Web Component để chèn vào website khác; thiết kế theo container (`@container`) thay vì chỉ theo viewport.
- shadcn-vue/Reka UI là thư viện headless nên responsive do Tailwind đảm nhiệm; dùng `h-dvh` cho khung chat trên mobile.
- Chưa có DataTable dựng sẵn, cần TanStack Table hoặc tự dựng cho các bảng quản trị.

## 7. Bảo mật và phân quyền

- Kiểm soát quyền truy cập tài liệu ở **backend** khi truy xuất, không dựa vào prompt.
- Chống prompt injection, che dữ liệu nhạy cảm (PII masking), rate limiting, audit log.
- Dữ liệu đưa ra LLM bên ngoài cần được rà soát theo chính sách dữ liệu.

## 8. Rủi ro và điểm cần lưu ý

| Vấn đề | Cách xử lý |
|---|---|
| Vector search của MariaDB còn mới (một index vector mỗi bảng, cột phải `NOT NULL`, lọc kết hợp yếu hơn pgvector) | Kiểm thử trên dữ liệu thật; bọc truy cập sau interface `VectorStore` để sau này đổi sang Qdrant/LanceDB |
| FULLTEXT tiếng Việt kém (tách từ theo khoảng trắng) | Tiền xử lý tách từ vào cột riêng, hợp nhất kết quả bằng RRF trong Go |
| Lọc theo quyền sau khi tìm vector có thể thiếu kết quả | Lấy dư ứng viên, theo dõi tỷ lệ bị loại |
| RAM của index vector khi dữ liệu lớn | Đặt `mhnsw_max_cache_size`, theo dõi và đo thực tế |
| JSON không phải JSONB | Tách trường phân quyền ra bảng quan hệ, dùng generated column cho trường cần lọc |

## 9. Câu hỏi cần chốt

1. Dùng LLM qua API hay tự host (ràng buộc về dữ liệu, chi phí GPU)?
2. Quy mô: số tài liệu/chunk, số người dùng đồng thời, RAM máy chủ dự kiến?
3. Đối tượng người dùng (nội bộ hay khách hàng bên ngoài) và có cần widget nhúng ngay từ đầu không?
4. Cần phân quyền theo tài liệu ở mức nào?
5. Phương án xác thực (Keycloak hay dùng hệ thống sẵn có)?

## 10. Lộ trình gợi ý

1. **MVP:** Go (Gin) + MariaDB + Redis + MinIO, LLM qua API, BGE-M3; chat cơ bản có streaming và trích dẫn nguồn.
2. **Tăng chất lượng:** hybrid search (vector + FULLTEXT + RRF), reranker, phân quyền theo tài liệu, feedback.
3. **Vận hành:** Langfuse, giám sát, đánh giá chất lượng RAG bằng bộ câu hỏi kiểm thử.
4. **Mở rộng:** widget nhúng, tự host LLM, tách Qdrant cho vector nếu dữ liệu lớn.
