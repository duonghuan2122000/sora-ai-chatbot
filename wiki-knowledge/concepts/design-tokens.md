---
title: Design tokens
date: 2026-10-04
tags: [design, token, css]
sources: [docs/tokens.css, docs/design-system.md]
---

# Design tokens

Giá trị thật trong [[tokens-css]] (`docs/tokens.css`), tương thích shadcn-vue (Reka UI) + Tailwind CSS v4. Quy tắc *dùng* chúng ở [[quy-tac-thiet-ke]].

## Bảng màu gốc

| Token | Hex | Vai trò |
|---|---|---|
| `--ink` | `#0F1B2D` | Chữ chính, bảng thương hiệu, hộp thoại tối |
| `--ink-2` | `#1B2B45` | Bề mặt phụ trên nền ink |
| `--mist` | `#F3F5F8` | Nền trang |
| `--paper` | `#FFFFFF` | Bề mặt (ô nhập, danh sách, hộp thoại, thanh bên) |
| `--blue` | `#2B3CE6` | Hành động chính, liên kết, mục đang chọn |
| `--blue-tint` | `#E8EBFD` | Nền mục đang chọn, bong bóng tin nhắn người dùng |
| `--highlight` | `#FFE66D` | Vệt bút dạ quang — xem quy tắc ở [[quy-tac-thiet-ke]] |
| `--muted-text` | `#566377` | Chữ phụ |
| `--placeholder` | `#6B778A` | Chữ gợi ý trong ô nhập |
| `--line` | `#CBD3DE` | Đường kẻ **trang trí** (1.5:1 — không dùng làm viền điều khiển) |
| `--input-line` | `#8A95A6` | Viền ô nhập/nút viền/checkbox (đạt 3:1) |
| `--danger` / `-tint` / `-line` | `#B8312A` / `#FCEDEB` / `#F0B9B4` | Lỗi, hành động hủy hoại |
| `--success` / `-tint` / `-line` | `#17784F` / `#E4F4EC` / `#A9D9C0` | Thành công, hoàn tất |
| `--warning` / `-tint` / `-line` | `#8A6100` / `#FFF4D6` / `#F0D27A` | Cảnh báo, đang chờ, khóa tạm |

Tương phản đã kiểm: ink/mist 15.8 (AAA), trắng/blue 7.3 (AAA), ink/highlight 13.8 (AAA), muted-text trên mist/paper 5.6/6.1 (AA), placeholder/paper 4.5 (AA), input-line/paper 3.0. Nút disabled giữ chữ `#66728A` trên `#E3E8EE`.

## Ánh xạ shadcn-vue

`--background`←mist · `--foreground`←ink · `--card`/`--popover`/`--secondary`/`--sidebar`←paper · `--primary`←blue · `--muted` `#E3E8EE` · `--muted-foreground`←muted-text · `--accent`←blue-tint (+ `--accent-foreground`←blue) · `--destructive`←danger · `--border`←line · `--input`←input-line · `--ring`←blue. Bộ `--sidebar-*` ánh xạ tương tự.

`@theme inline` phơi thêm cho Tailwind: `--color-ink`, `--color-highlight`, `--color-success*`, `--color-warning*`, `--color-danger-tint/line`, `--color-blue-tint`.

## Chữ

`--font-sans`: Inter Variable → Inter → Segoe UI → system-ui. `--font-mono`: JetBrains Mono (chỉ khối mã, ID, token).

| Token | Cỡ / dòng | Letter-spacing |
|---|---|---|
| `--text-display` | 30 / 1.2 | −0.02em |
| `--text-title` | 26 / 1.25 | −0.02em |
| `--text-heading` | 17 / 1.4 | −0.01em |
| `--text-body` | 15 / 1.55 | |
| `--text-small` | 13 / 1.5 | |
| `--text-micro` | 11 / 1.4 | |

## Hình khối và chuyển động

`--radius` 8px (điều khiển); `--radius-sm` 6 · `--radius-md` 8 · `--radius-lg` 12 · `--radius-xl` 16. `--shadow-overlay: 0 12px 32px -8px rgb(15 27 45 / 0.28)` — **chỉ** cho lớp phủ. `--duration-fast` 120ms · `--duration-base` 180ms · `--ease-out` `cubic-bezier(0.2, 0, 0, 1)`.

## Base layer và tiện ích

- `body`: nền `--background`, chữ `--foreground`, `font-feature-settings: "tnum" 0`.
- `input, textarea, select` **16px** mặc định (chống iOS tự phóng to), từ 768px trở lên dùng `--text-body`.
- `:focus-visible` — outline 2px `--ring` + offset 2; riêng input/textarea: bỏ outline, đổi viền `--ring` + quầng 3px `color-mix(ring 25%)`.
- `prefers-reduced-motion: reduce` → ép mọi animation/transition về 0.01ms.
- `.mark-hl` — vệt bút dạ quang bằng `linear-gradient` (14%→92%), chữ `ink`, có `box-decoration-break: clone` để vệt đúng khi ngắt dòng.
- `.tabular` — `font-variant-numeric: tabular-nums` cho số liệu bảng/quota/token.
