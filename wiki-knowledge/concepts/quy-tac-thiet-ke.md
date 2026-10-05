---
title: Quy tắc thiết kế (design system)
date: 2026-10-04
tags: [design, giao-dien]
sources: [docs/design-system.md]
---

# Quy tắc thiết kế

Biên soạn từ [[design-system]] (nguồn). Bắt buộc cho mọi màn hình M1–M8; **checklist mục 15 của nguồn là điều kiện hoàn thành**. Giá trị token cụ thể ở [[design-tokens]].

## Năm nguyên tắc

1. **Nguồn gốc là điểm nhấn** — sản phẩm khác ChatGPT ở chỗ câu trả lời có dẫn nguồn; vệt vàng chỉ dành cho nơi nhìn thấy nguồn/trích dẫn/mục đang chọn.
2. **Yên tĩnh trừ một chỗ** — tối đa **một** điểm nhấn mạnh mỗi màn hình; **một** nút primary mỗi vùng.
3. **Cấu trúc bằng khoảng trắng và đường kẻ** — `paper` đặt trực tiếp trên `mist`; **không lồng thẻ**; bóng đổ duy nhất `shadow-overlay` cho lớp phủ.
4. **Tiếng Việt là chuẩn** — font hỗ trợ tiếng Việt, giãn dòng rộng, dấu rõ ở cỡ nhỏ.
5. **Rõ ràng hơn khéo léo** — nút ghi "Lưu thay đổi", không ghi "Gửi".

## Ba khung bố cục (§4.2)

| Khung | Cấu trúc | Dùng cho |
|---|---|---|
| **Xác thực (chia đôi)** | Bảng thương hiệu `ink` 520 bên trái + cột biểu mẫu 400 trên `mist` bên phải; dưới 1024px bỏ bảng thương hiệu | Đăng nhập, đăng ký, quên mật khẩu |
| **Ứng dụng** | Thanh bên 248 nền `paper` + vùng nội dung `mist` | M2 chat, M4 tài liệu, cài đặt, quản trị |
| **Cài đặt (hai cột)** | Nội dung 760: cột nhãn 240 + cột biểu mẫu 460, cách nhau bằng đường kẻ | M1 cài đặt, M3 cấu hình |

Chiều rộng: biểu mẫu 400–460; danh sách/bảng ≤ 760; vùng chat ≤ 768 căn giữa.

## Màu — quy tắc dùng (§2.3)

`highlight` (`#FFE66D`) **chỉ** cho: vệt dưới tab đang chọn, đoạn trích nguồn, nền biểu tượng trung tính, điểm nhấn bảng thương hiệu. **Không** làm nền nút, không làm màu chữ trên nền sáng, không nhiều vệt trong cùng khối. Chữ trên nền vàng luôn là `ink`. Màu chỉ lấy từ bảng 2.1; **không bao giờ dùng màu làm tín hiệu duy nhất**.

## Hình khối (§5)

Bán kính: chip/checkbox 6 · nút/ô nhập/banner 8 · danh sách/vùng chứa/bảng 12 · hộp thoại/bong bóng chat/ô soạn tin 16. Focus: viền `blue` 2px + quầng 3px `blue` 25%. Chiều cao điều khiển: ô nhập 44 · nút lớn 46 · nút mặc định 44 · nút nhỏ 40.

## Thành phần riêng của sản phẩm (§8)

- **Tin nhắn chat (§8.1):** người dùng căn phải, bong bóng `blue-tint`, ≤ 80% rộng; **câu trả lời KHÔNG bong bóng**, căn trái, `max-w-[68ch]`, 15/1.65. Con trỏ streaming 2×18 `blue`, tắt khi `prefers-reduced-motion`.
- **Trích dẫn nguồn (§8.2):** chip cao 32 (số thứ tự trong vòng tròn `blue`, `file-text`, tên tệp rút gọn + "trang N"); đoạn trích bọc `.mark-hl`; chỉ số trong câu; bảng nguồn dạng Sheet bên phải; thiếu thông tin thì nói "Không tìm thấy trong tài liệu của bạn" và **không hiện chip**.
- **Ô soạn tin (§8.3):** tự giãn 1–8 dòng, nút gửi tròn 40 `blue`/`arrow-up`, `Enter` gửi `Shift+Enter` xuống dòng, luôn hiện phạm vi tài liệu khi ở chế độ theo tài liệu.
- **Công tắc chế độ (§8.4):** chip **Chat thông thường** / **Theo tài liệu**.
- **Markdown trong câu trả lời (§8.5):** h1–h3 cùng cỡ 17/700; mã dòng JetBrains Mono 13 trên `#E3E8EE`; khối mã nền `ink` chữ `#E6ECF5`.
- **Tải tài liệu (§8.6):** vùng thả nét đứt; huy hiệu Đang xử lý/Sẵn sàng/Lỗi; thanh hạn mức cao 6 — dưới 80% `blue`, từ 80% `warning`, 100% `danger`.

## Nội dung (§10)

Giọng ngắn, trung tính, xưng **"bạn"**; lỗi nói đã xảy ra gì + cách khắc phục, **không xin lỗi vòng vo**, không đổ lỗi người dùng. Sentence case, không viết hoa toàn bộ, **không nghiêng ở cỡ nhỏ**.

Từ vựng bắt buộc (§10.2): Đăng nhập/Đăng xuất/Đăng ký (không "Thoát", "Login") · Tài khoản · Hội thoại (không "Cuộc trò chuyện") · Trò chuyện (khi là mục điều hướng) · Tài liệu/tệp (không "File") · Nguồn/trích dẫn (không "reference") · Xóa (hủy hoại) vs Hủy (bỏ thao tác) · Thiết bị/phiên đăng nhập (không "Session") · Mô hình (không "Model") · Lượt tin nhắn/token.

Định dạng: ngày `11/10/2026`, giờ 24h `14:32`, tương đối khi dưới 7 ngày; dung lượng `48 MB`; số ngăn nghìn kiểu Việt `1.250`; tên tệp dài rút gọn **ở giữa, giữ đuôi**. Không dấu chấm than, không emoji trang trí (👍/👎 chỉ ở nút phản hồi).

## Trạng thái, chuyển động, trợ năng

- Thao tác ngắn → toast (Sonner, góc dưới phải); lỗi cần sửa → banner trong trang; lỗi ô nhập → thông báo dưới ô; hành động hủy hoại → hộp thoại nêu hậu quả + xác nhận bằng mật khẩu khi ảnh hưởng tài khoản.
- **Không chuyển động tự phát**; chuyển động đáp lại thao tác 120ms (màu/viền) hoặc 180ms `ease-out` (hộp thoại, ngăn kéo). Tôn trọng `prefers-reduced-motion`.
- Tương phản đạt AA (bảng 2.2 nguồn); focus thấy được trên mọi phần tử tương tác; nhãn gắn `for`/`id`, lỗi gắn `aria-describedby`, banner lỗi `role="alert"`; vùng chạm ≥ 44×44; `lang="vi"` trên thẻ `html`.

## Dùng trong code (§14)

`npm i @fontsource-variable/inter @fontsource-variable/jetbrains-mono`; `main.css`: `@import "tailwindcss"; @import "./tokens.css";` (xem thêm quy ước SCSS ở [[quy-uoc-style-frontend]] và tên class BEM `sora-` ở [[quy-uoc-dat-ten]]). Cài shadcn-vue: `button input label checkbox alert dialog tabs badge sonner sheet skeleton table`. Đặt lại biến thể `button` bằng `cva` (mã đầy đủ ở nguồn §14.2–14.3). Cấu trúc thư mục gợi ý: `styles/`, `components/ui/`, `components/app/` (`PasswordInput`, `PasswordStrength`, `CitationChip`, `SourceQuote`, `EmptyState`, `ConfirmDialog`, `SettingsSection`), `layouts/` (`AuthLayout`, `AppLayout`).
