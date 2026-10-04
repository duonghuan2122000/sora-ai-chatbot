# Hệ thống AI Chatbot hỏi đáp kiểu ChatGPT
## Nghiệp vụ chính và thứ tự ưu tiên (v2)

> **Phiên bản:** v2, cập nhật theo các quyết định mới của dự án
> **Nguồn:** `ai-chatbot-tong-quan.md` + các câu trả lời ở mục 2

---

## Mục lục

1. [Định hướng sản phẩm](#1-định-hướng-sản-phẩm)
2. [Các quyết định đã chốt](#2-các-quyết-định-đã-chốt)
3. [Các nghiệp vụ chính](#3-các-nghiệp-vụ-chính)
4. [Thứ tự ưu tiên](#4-thứ-tự-ưu-tiên)
5. [Rủi ro cần kiểm chứng sớm](#5-rủi-ro-cần-kiểm-chứng-sớm)
6. [Hạng mục bỏ hoặc hoãn](#6-hạng-mục-bỏ-hoặc-hoãn)
7. [Thay đổi so với v1](#7-thay-đổi-so-với-v1)
8. [Điểm còn mở](#8-điểm-còn-mở)

---

## 1. Định hướng sản phẩm

Website chatbot công khai, trải nghiệm giống ChatGPT hoặc DeepSeek, với hai chế độ chat:

| Chế độ | Mô tả |
|---|---|
| **Chat thông thường** | Hỏi đáp trực tiếp với LLM, không cần tài liệu. |
| **Chat theo tài liệu (RAG)** | Trả lời dựa trên kho tài liệu riêng của chính người dùng, kèm trích dẫn nguồn. |

Mỗi người dùng có thể **cấu hình mặc định** (prompt hướng dẫn riêng, mô hình, phạm vi tài liệu) áp dụng cho các cuộc hội thoại của mình.

---

## 2. Các quyết định đã chốt

| Câu hỏi | Quyết định |
|---|---|
| LLM | Dùng qua API |
| Quy mô | Chưa rõ, cần kiểm thử tải sớm |
| Đối tượng | Người dùng công khai trên website chatbot |
| Tài liệu | Tùy chỉnh theo từng người dùng (kho riêng) |
| Xác thực | Tự build từ đầu |
| Công nghệ nền | Go (Gin), MariaDB (VECTOR), Vue 3 + shadcn-vue + Tailwind CSS |

---

## 3. Các nghiệp vụ chính

### M1. Tài khoản và xác thực (tự build)
- Đăng ký, đăng nhập, đăng xuất, đổi mật khẩu, quên mật khẩu.
- JWT + refresh token, thu hồi phiên.
- Xác thực email.
- Băm mật khẩu bằng argon2 hoặc bcrypt, giới hạn số lần đăng nhập sai.
- Về sau: đăng nhập bằng Google/GitHub.

### M2. Chat
- Chat thông thường và chat theo tài liệu, người dùng chọn chế độ khi bắt đầu hoặc trong hội thoại.
- Streaming (SSE), hiển thị Markdown, dừng và tạo lại câu trả lời.
- Danh sách hội thoại: tạo, đổi tên, xóa, tìm kiếm, xem lại.
- Bọc LLM sau lớp trừu tượng nhà cung cấp, ghi nhận token mỗi lượt.

### M3. Cấu hình mặc định của người dùng
- Prompt hướng dẫn riêng (custom instructions) áp dụng cho mọi hội thoại mới.
- Mô hình mặc định, phạm vi tài liệu mặc định (tất cả, theo nhóm tài liệu, hoặc không dùng).
- Có thể ghi đè theo từng hội thoại.
- Giới hạn độ dài, đặt trong khối riêng của prompt, không được ghi đè quy tắc hệ thống.

### M4. Kho tài liệu cá nhân
- Tải lên, trích xuất văn bản, OCR tài liệu scan, chia chunk, embedding, lập chỉ mục.
- Theo dõi trạng thái nạp (đang xử lý, thành công, lỗi).
- Danh sách, xóa, nhóm tài liệu; xóa kèm chunk và vector tương ứng.
- Giới hạn dung lượng và số tài liệu mỗi người.
- Về sau: quản lý phiên bản tài liệu.

### M5. Truy xuất và trả lời có trích dẫn (RAG)
- Tìm kiếm trong tài liệu của chính người hỏi (và trong phạm vi đã chọn).
- Về sau: hybrid search (vector + FULLTEXT + RRF) và reranker.
- Trích dẫn nguồn (tài liệu, trang); trả lời "không biết" khi thiếu thông tin.

### M6. Cô lập dữ liệu và bảo mật
- Cô lập theo `user_id` ở mọi truy vấn, kiểm soát ở backend, không dựa vào prompt.
- Rate limiting, quota (tin nhắn, token, dung lượng).
- Chống prompt injection, kể cả từ nội dung tài liệu và từ prompt cấu hình của người dùng.
- Quét file tải lên, audit log, che dữ liệu nhạy cảm khi cần.
- Chính sách dữ liệu gửi ra LLM bên ngoài; xóa toàn bộ dữ liệu khi người dùng yêu cầu.

### M7. Quản trị
- Xem, khóa người dùng; xem mức sử dụng và chi phí.
- Về sau: thống kê feedback, cấu hình hệ thống qua giao diện admin.

### M8. Phản hồi, giám sát và đánh giá
- Phản hồi 👍/👎, xem nhật ký hội thoại.
- Giám sát hệ thống và chi phí (Prometheus/Grafana, Langfuse).
- Bộ câu hỏi kiểm thử để đánh giá chất lượng RAG.

---

## 4. Thứ tự ưu tiên

### 🔴 Mức 1: MVP

| # | Nghiệp vụ | Module | Ghi chú |
|:---:|---|:---:|---|
| 1 | Đăng ký, đăng nhập, JWT + refresh token, đổi mật khẩu | M1 | Làm đúng bảo mật ngay từ đầu |
| 2 | Chat thông thường: streaming, Markdown, danh sách hội thoại, dừng/tạo lại | M2 | Nền tảng cho mọi thứ khác |
| 3 | Lớp trừu tượng LLM API, ghi nhận token mỗi lượt | M2 | Dễ đổi nhà cung cấp, đo được chi phí |
| 4 | Cấu hình mặc định: prompt riêng của người dùng, ghi đè theo hội thoại | M3 | Cần xác định rõ vị trí trong prompt, xem mục 5 |
| 5 | Kho tài liệu cá nhân: tải lên, nạp dữ liệu, danh sách, xóa | M4 | Có giới hạn dung lượng |
| 6 | Chat theo tài liệu có trích dẫn, giới hạn trong tài liệu của người hỏi | M5 | Có công tắc chọn chế độ chat |
| 7 | **Cô lập dữ liệu theo `user_id`** ở mọi truy vấn, có kiểm thử riêng | M6 | Lỗi ở đây là lộ tài liệu giữa người dùng |
| 8 | Rate limiting và quota | M6 | Chi phí LLM do hệ thống chịu |
| 9 | Admin tối thiểu: xem, khóa người dùng, xem mức sử dụng | M7 | |

### 🟠 Mức 2: Trước khi mở rộng người dùng

| # | Nghiệp vụ | Module |
|:---:|---|:---:|
| 10 | Xác thực email, quên mật khẩu (lên mức 1 nếu mở đăng ký tự do) | M1 |
| 11 | Chống prompt injection (tài liệu và prompt cấu hình), quét file, audit log, chính sách dữ liệu ra LLM, xóa dữ liệu theo yêu cầu | M6 |
| 12 | Hybrid search + reranker (quan trọng với tiếng Việt) | M5 |
| 13 | Phản hồi 👍/👎 và xem nhật ký hội thoại | M8 |
| 14 | Giám sát chi phí và lỗi | M8 |
| 15 | Chọn phạm vi tài liệu theo nhóm, theo từng hội thoại | M3, M4 |

### 🟡 Mức 3: Hoàn thiện

| # | Nghiệp vụ | Module |
|:---:|---|:---:|
| 16 | Bộ câu hỏi kiểm thử, đánh giá chất lượng RAG | M8 |
| 17 | Gói dịch vụ và quota nâng cao (nếu có thu phí) | M7 |
| 18 | Đăng nhập Google/GitHub | M1 |
| 19 | Thống kê feedback, cấu hình qua giao diện admin | M7 |
| 20 | Quản lý phiên bản tài liệu | M4 |

---

## 5. Rủi ro cần kiểm chứng sớm

### 5.1. Vector search theo từng người dùng trên MariaDB

File gốc đã nêu MariaDB lọc kết hợp với vector search yếu hơn và lọc sau khi tìm có thể thiếu kết quả. Với kho tài liệu riêng từng người dùng, rủi ro này nặng hơn: top 50–100 vector gần nhất toàn cục phần lớn thuộc người khác, sau khi lọc theo `user_id` có thể còn rất ít hoặc không còn kết quả.

| Phương án | Khi nào hợp lý |
|---|---|
| Quét trực tiếp theo `user_id` (không dùng index vector) | Mỗi người dùng chỉ có ít chunk |
| Giữ MariaDB, lấy dư ứng viên lớn và theo dõi tỷ lệ bị loại | Quy mô nhỏ, chấp nhận độ chính xác thấp hơn |
| Chuyển sang Qdrant (lọc theo payload) | Nhiều người dùng, nhiều chunk |

Nên bọc sau interface `VectorStore` và làm thử nghiệm tải ở giai đoạn MVP. Việc có tách Qdrant hay không là **quyết định cần chốt sớm**, không còn là mục mở rộng.

### 5.2. Prompt cấu hình của người dùng

Prompt này do người dùng viết nên phải coi là dữ liệu không tin cậy:

- Đặt trong khối riêng, sau quy tắc hệ thống, có giới hạn độ dài.
- Không cho phép ghi đè quy tắc an toàn, quy tắc trích dẫn hoặc phạm vi truy cập dữ liệu.
- Phạm vi tài liệu là quyết định của backend theo `user_id`, không phụ thuộc nội dung prompt.

### 5.3. Chi phí LLM

Khi mở công khai, chi phí tăng theo mức sử dụng. Cần quota theo người dùng, ghi nhận token từng lượt, và cảnh báo khi vượt ngưỡng chi phí.

---

## 6. Hạng mục bỏ hoặc hoãn

| Hạng mục | Lý do |
|---|---|
| Widget nhúng | Sản phẩm là website riêng; chỉ làm nếu sau này có nhu cầu |
| Tự host LLM | Đã chọn dùng API |
| Keycloak | Đã chọn tự build xác thực |
| `document_acl` theo nhóm, vai trò phức tạp | Thay bằng cô lập theo chủ sở hữu (`user_id`) |

---

## 7. Thay đổi so với v1

| Nội dung | v1 | v2 |
|---|---|---|
| Bối cảnh | Hệ thống nội bộ | Sản phẩm công khai kiểu ChatGPT |
| Phân quyền | `document_acl` theo nhóm | Cô lập theo `user_id`, bắt buộc từ MVP |
| Xác thực | Keycloak hoặc hệ thống sẵn có | Tự build, lên mức 1 với phạm vi lớn hơn |
| Rate limit, quota | Mức 2 | Mức 1 (bảo vệ chi phí) |
| Chế độ chat | Chỉ hỏi đáp theo tài liệu | Chat thông thường + chat theo tài liệu |
| Cấu hình người dùng | Không có | Module M3, mức 1 |
| Widget nhúng | Mức 4 | Bỏ hoặc hoãn |
| Qdrant | Mở rộng | Quyết định cần chốt ở MVP |

---

## 8. Điểm còn mở

- [ ] Phạm vi "cấu hình mặc định": chỉ prompt hướng dẫn, hay gồm cả mô hình, nhiệt độ, phạm vi tài liệu?
- [ ] Người dùng chọn tài liệu nào dùng cho từng hội thoại, hay áp dụng toàn bộ kho tài liệu?
- [ ] Có nhiều mô hình LLM cho người dùng chọn không, hay chỉ một?
- [ ] Mở đăng ký tự do hay theo lời mời (ảnh hưởng đến mức ưu tiên của xác thực email và quota)?
- [ ] Có thu phí hoặc chia gói dịch vụ không?
- [ ] Quy mô dự kiến: số người dùng, số tài liệu mỗi người, để chốt phương án vector search.
