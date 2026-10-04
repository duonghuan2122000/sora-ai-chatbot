# Index — Wiki Sora AI Chatbot

Catalog mọi page. Cập nhật mỗi lần thêm page. Ngày biên soạn gần nhất: 2026-10-04.

## Sources — tóm tắt tài liệu nguồn

- [[tong-quan-v1]] — bản nháp v1: kiến trúc, luồng RAG, rủi ro kỹ thuật (đã bị v2 thay một phần)
- [[nghiep-vu-uu-tien-v2]] — **nguồn sự thật**: nghiệp vụ M1–M8 và thứ tự ưu tiên 3 mức
- [[design-system]] — nguồn quy tắc thị giác và nội dung cho mọi màn hình
- [[tokens-css]] — nguồn `tokens.css`
- [[m1-spec]] — đặc tả chi tiết M1: schema SQL, API, chính sách bảo mật, kế hoạch

## Concepts — khái niệm và quyết định kỹ thuật

- [[kien-truc-he-thong]] — sơ đồ thành phần, SPA + Go API + MariaDB/Redis/MinIO/Asynq
- [[ngan-xep-ky-thuat]] — stack đã chốt và trạng thái code hiện tại
- [[rag]] — luồng nạp tài liệu và luồng hỏi đáp có trích dẫn
- [[vector-search-mariadb]] — rủi ro lớn nhất của MVP và ba phương án
- [[co-lap-du-lieu-theo-user-id]] — quy tắc bất di bất dịch, ưu tiên #7
- [[thiet-ke-du-lieu-mariadb]] — UUIDv7, UTC, JSON, cascade, khóa Redis
- [[xac-thuc-va-token]] — JWT Ed25519, refresh token xoay vòng, thu hồi tức thời
- [[mat-khau-va-chong-lam-dung]] — argon2id, chính sách mật khẩu, rate limit, CAPTCHA
- [[chong-do-email]] — phản hồi đồng nhất 202, chống dò thời gian
- [[bao-mat-ung-dung]] — cookie/CSRF/header, prompt injection, quét tệp, audit log
- [[mo-hinh-token-frontend]] — access token trong bộ nhớ, single-flight, nhiều tab
- [[quy-tac-thiet-ke]] — ba khung bố cục, hình khối, thành phần, giọng văn, từ vựng
- [[design-tokens]] — bảng token gốc: màu, chữ, bán kính, chuyển động, ánh xạ shadcn-vue

## Entities — nghiệp vụ và màn hình

- [[modules-m1-m8]] — toàn cảnh tám module, tra cứu nhanh
- [[m1-tai-khoan-va-xac-thuc]] — module M1: phạm vi, API, luồng, kế hoạch
- [[man-hinh-m1]] — 14 màn hình M1 và trạng thái của chúng

## Decisions — điểm mở và thay đổi

- [[diem-con-mo]] — các câu hỏi chưa chốt, gộp từ v2 §8 và M1 §14
- [[thay-doi-v1-sang-v2]] — v1 → v2 thay đổi gì và vì sao
