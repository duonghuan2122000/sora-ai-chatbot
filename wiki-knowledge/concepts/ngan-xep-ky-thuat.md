---
title: Ngăn xếp kỹ thuật
date: 2026-10-04
tags: [stack, trang-thai]
sources: [docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md, docs/ai-chatbot-tong-quan.md, docs/auth/m1-tai-khoan-va-xac-thuc.md]
---

# Ngăn xếp kỹ thuật

## Đã chốt

| Lớp | Lựa chọn | Ghi chú |
|---|---|---|
| Backend | **Go + Gin** | Cấu trúc `internal/` theo [[m1-tai-khoan-va-xac-thuc]] §8.1 |
| CSDL | **MariaDB ≥ 11.8 LTS**, có kiểu `VECTOR`, `utf8mb4` | Rủi ro ở [[vector-search-mariadb]] |
| Cache/queue | **Redis**, **Asynq** | Rate limit, sid thu hồi, cooldown email; hàng đợi email + ingestion |
| Lưu tệp | **MinIO** (tương thích S3) | |
| Frontend | **Vue 3 + Vite**, **Pinia**, **vue-router** | |
| UI | **shadcn-vue (Reka UI)**, **Tailwind CSS v4**, Inter + JetBrains Mono qua `@fontsource`, **Lucide** | Xem [[design-tokens]] |
| Style | **SCSS** (nested) + `base.scss` cho biến dùng chung → [[quy-uoc-style-frontend]] | Class theo BEM tiền tố `sora-` → [[quy-uoc-dat-ten]] |
| LLM | Qua API, **chưa chốt nhà cung cấp**, bọc sau lớp trừu tượng | |
| Xác thực | Tự build (không Keycloak) | [[xac-thuc-va-token]] |

## Chưa chốt (gợi ý từ v1)

Embedding **BGE-M3** (1024 chiều) hoặc multilingual-e5 · reranker `bge-reranker-v2-m3` · xử lý tài liệu Docling/Apache Tika + PaddleOCR cho bản scan · giám sát Langfuse, Prometheus/Grafana, OpenTelemetry · CAPTCHA Turnstile hay hCaptcha · nhà cung cấp email.

## Trạng thái code hiện tại (quan trọng)

Dự án **spec-first**: quyết định nằm hết trong `docs/`, code gần như chưa có.

- `app/backend/` — **trống hoàn toàn**, chưa có `go.mod`.
- `app/frontend/` — scaffold `npm create vue@latest` nguyên bản; `HelloWorld`, `TheWelcome`, `AboutView`, `stores/counter.js`, `e2e/vue.spec.js` là rác mẫu, xóa khi làm M1.
- Chưa cài Tailwind/shadcn — đó là bước đầu của M1.

**Không suy đoán cấu trúc thư mục từ code.** Dựng theo spec: Go ở [[m1-tai-khoan-va-xac-thuc]] §8.1, Vue ở §9 + [[quy-tac-thiet-ke]] §14.4.

## Lệnh (frontend, chạy trong `app/frontend/`)

`npm install` · `npm run dev` (5173) · `npm run build` · `npm run test:unit` (vitest/jsdom, quét `src/**`, loại `e2e/`) · `npm run test:e2e` (playwright, cần `npx playwright install` lần đầu) · `npm run format`.

Unit test đặt cạnh code trong `__tests__/`; e2e trong `app/frontend/e2e/`. Backend chưa có lệnh nào.
