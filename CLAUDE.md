# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Trạng thái hiện tại (quan trọng)

Đây là dự án **spec-first**: quyết định nằm trong `docs/`, đã được biên soạn thành wiki ở `wiki-knowledge/`; code gần như chưa có.

- `app/backend/` — **trống hoàn toàn**. Chưa có `go.mod`, chưa có dòng code nào.
- `app/frontend/` — vẫn là scaffold `npm create vue@latest` nguyên bản (`HelloWorld`, `TheWelcome`, `AboutView`, `stores/counter.js`, `e2e/vue.spec.js` đều là rác mẫu, sẽ bị xóa khi làm M1).

Vì vậy **không** suy đoán cấu trúc thư mục Go hay Vue từ code — dựng theo spec đã biên soạn trong wiki: [[m1-tai-khoan-va-xac-thuc]] mục 8.1 cho Go, mục 9 + [[quy-tac-thiet-ke]] §14.4 cho Vue. Cần nguyên văn thì mở `docs/auth/m1-tai-khoan-va-xac-thuc.md`.

## Nguồn sự thật — đọc wiki, không quét `docs/`

`wiki-knowledge/` là tầng **biên soạn sẵn** từ `docs/`: kiến thức đã tách theo chủ đề, liên kết chéo, đánh dấu mâu thuẫn giữa các phiên bản tài liệu. `docs/` là **nguồn thô** (không sửa), wiki là bản đồ của nó.

**Mặc định trả lời bằng wiki** + citation `[[tên-page]]`. Chỉ mở `docs/` khi cần **nguyên văn** (SQL, mã Go/Vue mẫu, bảng đầy đủ) — mỗi page wiki đã trỏ tới đúng mục của nguồn. Bắt đầu ở `wiki-knowledge/index.md`.

| Chủ đề | Page wiki | Nguồn gốc (chỉ mở khi cần nguyên văn) |
|---|---|---|
| Nghiệp vụ chốt, thứ tự ưu tiên M1–M8 | [[nghiep-vu-uu-tien-v2]], [[modules-m1-m8]] | `docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md` — **thắng** khi mâu thuẫn với bản cũ |
| Kiến trúc, luồng RAG, rủi ro kỹ thuật | [[kien-truc-he-thong]], [[rag]], [[vector-search-mariadb]] | `docs/ai-chatbot-tong-quan.md` (v1 — một phần đã bị v2 thay: bỏ `document_acl`, Keycloak, widget nhúng; phân quyền nay cô lập theo `user_id`) |
| Design system, token | [[quy-tac-thiet-ke]], [[design-tokens]] | `docs/design-system.md`, `docs/tokens.css` |
| M1 — tài khoản và xác thực | [[m1-tai-khoan-va-xac-thuc]], [[man-hinh-m1]] | `docs/auth/m1-tai-khoan-va-xac-thuc.md`, `docs/auth/*.svg` |
| Quy ước code (đặt tên, SCSS, khóa ngoại) | [[quy-uoc-dat-ten]], [[quy-uoc-style-frontend]], [[thiet-ke-du-lieu-mariadb]] | `docs/quy-uoc-code.md` |
| Điểm còn mở, thay đổi v1→v2 | [[diem-con-mo]], [[thay-doi-v1-sang-v2]] | mục 8 của v2 và mục 14 của spec M1 |

- Quy ước viết wiki ở `wiki-knowledge/CLAUDE.md` (frontmatter, cách đánh dấu mâu thuẫn, định dạng `log.md`).
- **Khi `docs/` đổi, wiki phải được ingest lại** (skill `sora-wiki`): cập nhật page nguồn + mọi page liên quan, ghi `log.md`. Đừng để wiki lệch khỏi nguồn — nó là thứ được đọc trước.

## Ngăn xếp đã chốt

Backend: **Go + Gin**, **MariaDB** (bản có kiểu `VECTOR`, ≥ 11.8 LTS, `utf8mb4`), **Redis**, **MinIO**, **Asynq** (hàng đợi email/ingestion).
Frontend: **Vue 3 + Vite**, **Pinia**, **vue-router**, **shadcn-vue (Reka UI)**, **Tailwind CSS v4**, Inter + JetBrains Mono qua `@fontsource`, icon **Lucide** (`lucide-vue-next`).
LLM: gọi qua API (chưa chốt nhà cung cấp), bọc sau lớp trừu tượng.

Frontend chưa cài Tailwind/shadcn/SCSS (`sass`) — đó là bước đầu khi làm M1. Quy ước style (SCSS nested, `base.scss`, BEM `sora-`) ở [[quy-uoc-style-frontend]] và [[quy-uoc-dat-ten]].

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

Ngoài ra có skill `sora-wiki` — biên soạn và bảo trì `wiki-knowledge/` (xem mục "Nguồn sự thật" ở trên): ingest nguồn mới, trả lời câu hỏi dựa trên wiki, lint sức khỏe wiki. Gọi skill này khi `docs/` thay đổi.

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

Đọc [[quy-tac-thiet-ke]] + [[design-tokens]] trước khi dựng màn hình mới; checklist mục 15 của nguồn là điều kiện hoàn thành (nguyên văn ở `docs/design-system.md`).

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

Bản gộp đầy đủ ở `wiki-knowledge/decisions/diem-con-mo.md` (chính sách đăng ký mở/lời mời, chặn tài khoản chưa xác thực đến mức nào, nhà cung cấp email, Turnstile hay hCaptcha, SPA và API cùng site hay khác site, quy mô dự kiến). Nguồn gốc: mục 8 của `nghiep-vu-va-uu-tien-v2.md` và mục 14 của spec M1. Đừng tự chốt thay người dùng.
