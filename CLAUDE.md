# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Trạng thái hiện tại (quan trọng)

Đây là dự án **spec-first**: toàn bộ quyết định nằm trong `docs/`, code gần như chưa có.

- `app/backend/` — **trống hoàn toàn**. Chưa có `go.mod`, chưa có dòng code nào.
- `app/frontend/` — vẫn là scaffold `npm create vue@latest` nguyên bản (`HelloWorld`, `TheWelcome`, `AboutView`, `stores/counter.js`, `e2e/vue.spec.js` đều là rác mẫu, sẽ bị xóa khi làm M1).

Vì vậy **không** suy đoán cấu trúc thư mục Go hay Vue từ code — đọc `docs/` trước. Khi bắt đầu viết code, dựng theo cấu trúc đã ghi trong spec (`docs/auth/m1-tai-khoan-va-xac-thuc.md` mục 8.1 cho Go, mục 9 + `docs/design-system.md` mục 14.4 cho Vue).

## Nguồn sự thật — đọc theo thứ tự này

| File | Nội dung |
|---|---|
| `docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md` | **Nghiệp vụ chốt + thứ tự ưu tiên.** M1–M8, mức 1/2/3. Ưu tiên file này khi mâu thuẫn với bản cũ. |
| `docs/ai-chatbot-tong-quan.md` | Kiến trúc, luồng RAG, rủi ro kỹ thuật (bản nháp v1 — một phần đã bị v2 thay) |
| `docs/design-system.md` | Quy tắc thị giác + nội dung, bắt buộc cho mọi màn hình |
| `docs/tokens.css` | Token thật (biến CSS + ánh xạ shadcn-vue) |
| `docs/auth/m1-tai-khoan-va-xac-thuc.md` | Đặc tả chi tiết M1: schema SQL, API, chính sách bảo mật, kế hoạch |
| `docs/auth/*.svg` | 14 màn hình M1 (nguồn thị giác để dựng UI) |

Bản v1 (`tong-quan.md`) còn mô tả `document_acl` theo nhóm/quyền, Keycloak, widget nhúng — **v2 đã bỏ hết**. Phân quyền nay là cô lập theo `user_id`, xác thực tự build.

## Ngăn xếp đã chốt

Backend: **Go + Gin**, **MariaDB** (bản có kiểu `VECTOR`, ≥ 11.8 LTS, `utf8mb4`), **Redis**, **MinIO**, **Asynq** (hàng đợi email/ingestion).
Frontend: **Vue 3 + Vite**, **Pinia**, **vue-router**, **shadcn-vue (Reka UI)**, **Tailwind CSS v4**, Inter + JetBrains Mono qua `@fontsource`, icon **Lucide** (`lucide-vue-next`).
LLM: gọi qua API (chưa chốt nhà cung cấp), bọc sau lớp trừu tượng.

Frontend chưa cài Tailwind/shadcn — đó là bước đầu khi làm M1.

## Lệnh

Frontend (chạy trong `app/frontend/`):
```bash
npm install
npm run dev          # vite dev, cổng 5173
npm run build
npm run test:unit    # vitest (jsdom), quét src/**, loại e2e/
npm run test:unit -- src/components/__tests__/HelloWorld.spec.js   # một file
npm run test:e2e     # playwright, cần `npx playwright install` lần đầu
npm run format       # prettier --write src/
```
Unit test đặt cạnh code trong `__tests__/`; e2e trong `app/frontend/e2e/`. Playwright tự chạy dev server (preview trên CI).

Backend: chưa có lệnh nào để chạy. Không bịa.

## Quy trình làm việc

Có bộ lệnh spec-driven theo PBI (số), dùng `.specify/specs/<pbi>/`:

```
/sora-spec <pbi> <mô tả>   → spec.md + checklists/requirements.md (CÁI GÌ / TẠI SAO, không kỹ thuật)
/sora-plan <pbi>           → plan.md (kế hoạch kỹ thuật)
/sora-task <pbi>           → tasks.md
/sora-implement <pbi>      → thi công toàn bộ tasks.md
```

Ngoài ra có skill `sora-wiki` (wiki liên kết chéo trong `wiki/`, dùng `index.md` + `log.md` + `CLAUDE.md` riêng).

## Quy tắc bất di bất dịch

**Cô lập dữ liệu (M6, ưu tiên #7).** Lấy `uid` từ context của middleware (JWT claim `sub`), **không bao giờ** nhận `user_id` từ client. Mọi truy vấn tài liệu/chunk/hội thoại đều lọc theo `uid`. Đây là hạng mục có kiểm thử riêng vì lỗi ở đây là lộ tài liệu giữa người dùng.

**Prompt người dùng là dữ liệu không tin cậy.** Prompt cấu hình do người dùng viết (M3) đặt trong khối riêng, sau quy tắc hệ thống, có giới hạn độ dài, không được ghi đè quy tắc an toàn/trích dẫn. Phạm vi tài liệu do backend quyết theo `uid`, không phụ thuộc nội dung prompt. Nội dung tài liệu cũng là nguồn injection.

**Bí mật chỉ lưu dạng băm.** Mật khẩu argon2id (`m=64MiB, t=3, p=2`, chuỗi PHC, có rehash khi đổi tham số), refresh token SHA-256, token email SHA-256. Không ghi mật khẩu/token vào log hay `audit_logs.metadata`.

**Chống dò email.** Đăng ký / quên mật khẩu / gửi lại xác thực luôn trả **202 cùng nội dung**, việc gửi email đẩy vào Asynq (không đồng bộ, để độ trễ hai nhánh gần nhau). Đăng nhập khi không có user vẫn chạy `Verify` với hash giả.

**Token đặt trong fragment** (`#token=...`), không dùng query string — tránh lọt vào log và `Referer`. Frontend xóa token khỏi URL bằng `history.replaceState`.

## Mô hình token phía frontend (dễ làm sai)

- Access token (JWT Ed25519, 15 phút) **chỉ giữ trong bộ nhớ** Pinia store — không `localStorage`. Tải lại trang là mất, nên lúc khởi động app gọi `POST /auth/refresh` để lấy token mới từ cookie; router phải **chờ** bước này xong trước khi chạy guard (tránh nhấp nháy về trang đăng nhập).
- Refresh token nằm trong cookie `HttpOnly; Secure; SameSite=Strict; Path=/api/v1/auth` → chỉ `/auth/refresh` và `/auth/logout` dùng cookie, các route khác dùng `Authorization: Bearer`.
- HTTP client: gặp 401 thì chạy **một** lần refresh dùng chung (single-flight) rồi thử lại; nhiều tab bọc trong `navigator.locks.request('auth-refresh', …)`, đồng bộ đăng xuất qua `BroadcastChannel`.
- SSE của chat dùng `fetch` + `ReadableStream` (EventSource không gửi được header `Authorization`).

## Giao diện — luật của design system

Đọc `docs/design-system.md` trước khi dựng màn hình mới; checklist ở mục 15 là điều kiện hoàn thành.

- **Ba khung bố cục** (mục 4.2): Xác thực (chia đôi 520 + 400), Ứng dụng (thanh bên 248 + nội dung), Cài đặt (hai cột 240 + 460).
- **Một điểm nhấn mỗi vùng**: chỉ một nút primary; `highlight` (`#FFE66D`) chỉ dùng cho trích dẫn nguồn, tab đang chọn, vệt nhấn — không làm nền nút. Màu chỉ lấy từ bảng 2.1.
- **Không lồng thẻ**; phân nhóm bằng khoảng trắng và đường kẻ `line`. Bóng đổ duy nhất `shadow-overlay` cho lớp phủ.
- **Tiếng Việt**: sentence case, không viết hoa toàn bộ, không nghiêng ở cỡ nhỏ. Từ vựng theo bảng 10.2 (Đăng nhập/Tài khoản/Hội thoại/Tài liệu/Nguồn — không dùng Login/Account/Chat/File/reference). Giọng "bạn", lỗi nói rõ đã xảy ra gì + cách khắc phục, không xin lỗi vòng vo, không lộ thông tin cho kẻ tấn công.
- Nhãn ô nhập **luôn hiện phía trên** (không dùng placeholder thay nhãn); lỗi có biểu tượng + chữ; ô nhập 16px trên di động; vùng chạm ≥ 44×44; `lang="vi"`.
- Nội dung chat: câu trả lời **không bong bóng**, `max-w-[68ch]`; tin nhắn người dùng bong bóng `blue-tint` căn phải. Trích dẫn dùng chip + `.mark-hl` (mục 8.2).
- Không chuyển động tự phát; tôn trọng `prefers-reduced-motion`.

## Dữ liệu

- ID là `UUID` của MariaDB, sinh **UUIDv7 phía Go** (`google/uuid`).
- Thời gian lưu UTC (`DATETIME(3)`), hiển thị theo múi giờ ở frontend.
- Metadata linh hoạt để trong `JSON`; trường cần lọc nhanh thì dùng generated column có index (JSON của MariaDB không phải JSONB).
- Xóa theo cascade: `users` → `sessions`, `auth_tokens`; `audit_logs.user_id` đặt NULL để giữ bản ghi ẩn danh.
- Vector: bọc truy cập sau interface `VectorStore` — việc giữ MariaDB hay tách Qdrant là **quyết định mở ở MVP**, không phải hạng mục mở rộng. Rủi ro: top 50–100 vector gần nhất toàn cục phần lớn thuộc người khác, sau khi lọc `user_id` có thể hết kết quả.

## Còn mở

Xem mục 8 của `nghiep-vu-va-uu-tien-v2.md` và mục 14 của spec M1 (chính sách đăng ký mở/lời mời, chặn tài khoản chưa xác thực đến mức nào, nhà cung cấp email, Turnstile hay hCaptcha, SPA và API cùng site hay khác site). Đừng tự chốt thay người dùng.
