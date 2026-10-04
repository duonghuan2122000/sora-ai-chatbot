# Design system chung cho hệ thống chatbot (Sora AI Chatbot)

> **Mục đích:** một bộ quy tắc thị giác và nội dung duy nhất cho mọi nghiệp vụ (M1 đến M8), để các màn hình thiết kế sau này giống như cùng một sản phẩm.
> **Nguồn:** rút ra từ 14 màn hình M1 đã thiết kế, có chỉnh lại màu để đạt chuẩn tương phản.
> **File đi kèm:** `tokens.css` (biến CSS cho shadcn-vue + Tailwind v4), `design-system.svg` (bảng mẫu trực quan).

---

## Mục lục

1. [Nguyên tắc](#1-nguyên-tắc)
2. [Màu sắc](#2-màu-sắc)
3. [Chữ](#3-chữ)
4. [Khoảng cách và bố cục](#4-khoảng-cách-và-bố-cục)
5. [Hình khối](#5-hình-khối)
6. [Biểu tượng](#6-biểu-tượng)
7. [Thành phần](#7-thành-phần)
8. [Thành phần riêng của sản phẩm](#8-thành-phần-riêng-của-sản-phẩm)
9. [Mẫu màn hình theo nghiệp vụ](#9-mẫu-màn-hình-theo-nghiệp-vụ)
10. [Cách viết nội dung](#10-cách-viết-nội-dung)
11. [Trạng thái và phản hồi](#11-trạng-thái-và-phản-hồi)
12. [Chuyển động](#12-chuyển-động)
13. [Trợ năng](#13-trợ-năng)
14. [Dùng trong code](#14-dùng-trong-code)
15. [Danh sách kiểm tra cho màn hình mới](#15-danh-sách-kiểm-tra-cho-màn-hình-mới)

---

## 1. Nguyên tắc

| # | Nguyên tắc | Hệ quả khi thiết kế |
|:---:|---|---|
| 1 | **Nguồn gốc là điểm nhấn.** Sản phẩm khác ChatGPT ở chỗ câu trả lời có dẫn nguồn từ tài liệu của người dùng. | Vệt vàng "bút dạ quang" chỉ dành cho nơi nhìn thấy nguồn, trích dẫn và mục đang chọn. |
| 2 | **Yên tĩnh trừ một chỗ.** Mỗi màn hình có tối đa một điểm nhấn mạnh. | Nút xanh chính chỉ có một mỗi vùng; không dùng nhiều màu nhấn cạnh nhau. |
| 3 | **Cấu trúc bằng khoảng trắng và đường kẻ**, không bằng thẻ lồng thẻ. | Không đặt card trong card; bóng đổ chỉ dành cho lớp phủ. |
| 4 | **Tiếng Việt là chuẩn.** Dấu phải đọc rõ ở mọi cỡ chữ. | Font hỗ trợ tiếng Việt gốc, giãn dòng rộng, không viết hoa toàn bộ. |
| 5 | **Rõ ràng hơn khéo léo.** Gọi tên theo cách người dùng hiểu, hành động nói đúng việc sẽ xảy ra. | Nút ghi "Lưu thay đổi", không ghi "Gửi". |

---

## 2. Màu sắc

### 2.1. Bảng màu

| Token | Hex | Vai trò |
|---|---|---|
| `ink` | `#0F1B2D` | Chữ chính, bảng thương hiệu, hộp thoại tối |
| `ink-2` | `#1B2B45` | Bề mặt phụ trên nền ink |
| `mist` | `#F3F5F8` | Nền trang |
| `paper` | `#FFFFFF` | Bề mặt: ô nhập, danh sách, hộp thoại, thanh bên |
| `blue` (ultramarine) | `#2B3CE6` | Hành động chính, liên kết, mục điều hướng đang chọn |
| `blue-tint` | `#E8EBFD` | Nền mục đang chọn, bong bóng tin nhắn của người dùng |
| `highlight` | `#FFE66D` | Vệt bút dạ quang (xem 2.3) |
| `muted-text` | `#566377` | Chữ phụ, mô tả |
| `placeholder` | `#6B778A` | Chữ gợi ý trong ô nhập |
| `line` | `#CBD3DE` | Đường kẻ phân cách, viền trang trí |
| `input-line` | `#8A95A6` | Viền ô nhập, nút viền, checkbox (đạt 3:1) |
| `danger` | `#B8312A` | Lỗi, hành động hủy hoại; tint `#FCEDEB`, viền `#F0B9B4` |
| `success` | `#17784F` | Thành công, trạng thái hoàn tất; tint `#E4F4EC`, viền `#A9D9C0` |
| `warning` | `#8A6100` | Cảnh báo, đang chờ; tint `#FFF4D6`, viền `#F0D27A` |

### 2.2. Độ tương phản đã kiểm tra (WCAG)

| Cặp màu | Tỷ lệ | Mức |
|---|:---:|---|
| ink trên mist | 15.8 | AAA |
| muted-text trên mist / paper | 5.6 / 6.1 | AA |
| trắng trên blue | 7.3 | AAA |
| blue trên mist | 6.7 | AA |
| ink trên highlight | 13.8 | AAA |
| trắng trên danger / danger trên paper | 6.0 / 6.0 | AA |
| danger trên danger-tint | 5.3 | AA |
| success trên paper / success-tint | 5.5 / 4.8 | AA |
| warning trên warning-tint | 5.1 | AA |
| placeholder trên paper | 4.5 | AA |
| input-line trên paper (viền điều khiển) | 3.0 | đạt 3:1 cho thành phần giao diện |

> **Lưu ý:** `line` (`#CBD3DE`, 1.5:1) chỉ dùng cho đường phân cách trang trí, không dùng làm viền ô nhập hay nút. Nút "đã vô hiệu hóa" được miễn yêu cầu tương phản nhưng vẫn giữ chữ `#66728A` trên `#E3E8EE` để đọc được.

### 2.3. Quy tắc dùng màu

| Màu | Được dùng | Không dùng |
|---|---|---|
| `blue` | Nút chính (mỗi vùng một nút), liên kết, vòng focus, mục chọn, checkbox đã chọn | Trang trí, nền mảng lớn |
| `highlight` | Vệt dưới tab đang chọn; đoạn trích nguồn trong câu trả lời; nền biểu tượng trạng thái trung tính (ví dụ phong bì email); điểm nhấn trên bảng thương hiệu | Nền nút, màu chữ trên nền sáng, viền, nhiều vệt trong cùng một khối. Chữ trên nền vàng luôn là `ink` |
| `danger` | Lỗi, xóa vĩnh viễn, đăng xuất tất cả | Nhấn mạnh thông thường |
| `success` | Đã xác thực, xử lý xong, "Thiết bị này" | Nút chính |
| `warning` | Cảnh báo, hết hạn, khóa tạm, đang chờ | Lỗi (dùng `danger`) |

Không bao giờ dùng màu làm tín hiệu duy nhất: lỗi có biểu tượng và chữ, trạng thái có nhãn.

---

## 3. Chữ

**Họ chữ chính:** Inter (hỗ trợ đầy đủ ký tự tiếng Việt, dấu rõ ở cỡ nhỏ). Dự phòng: Segoe UI, system-ui. Chữ từ 17px trở lên thu hẹp khoảng cách chữ nhẹ (`-0.01em`; từ 24px là `-0.02em`) để tiêu đề gọn hơn.
**Chữ đơn cách:** JetBrains Mono, chỉ dùng cho khối mã trong câu trả lời, ID và token.

### 3.1. Thang chữ

| Tên | Cỡ / dòng | Đậm | Dùng cho | Lớp Tailwind |
|---|---|:---:|---|---|
| Display | 30 / 1.2 | 700 | Tiêu đề màn hình xác thực | `text-display font-bold` |
| Title | 26 / 1.25 | 700 | Tiêu đề trang trong ứng dụng | `text-title font-bold` |
| Heading | 17 / 1.4 | 700 | Tiêu đề mục, hộp thoại (hộp thoại dùng 21) | `text-heading font-bold` |
| Body | 15 / 1.55 | 400 | Nội dung chính, ô nhập, nút | `text-body` |
| Label | 13 / 1.5 | 600 | Nhãn ô nhập, tên cột | `text-small font-semibold` |
| Small | 13 / 1.5 | 400 | Mô tả, trợ giúp, chữ phụ | `text-small text-muted-foreground` |
| Micro | 11 / 1.4 | 400 | Ghi chú nhỏ, tên nhà cung cấp | `text-micro` |

### 3.2. Quy tắc

- Giữ **sentence case** (chỉ viết hoa chữ đầu câu và danh từ riêng). Không viết hoa toàn bộ, không đặt nhãn nhỏ viết hoa phía trên tiêu đề.
- Độ dài dòng tối đa khoảng 70 ký tự cho đoạn văn; câu trả lời của chatbot giới hạn `max-w-[68ch]`.
- Giãn dòng body tối thiểu 1.5 để dấu tiếng Việt không dính dòng trên.
- Chỉ nhấn mạnh bằng đậm (600), không dùng nghiêng cho tiếng Việt ở cỡ nhỏ. Không làm nổi bật một từ đơn lẻ trong tiêu đề bằng màu.
- Số liệu trong bảng, quota, token dùng `tabular-nums`.
- Ô nhập có cỡ chữ 16px trên di động (tránh iOS tự phóng to).

---

## 4. Khoảng cách và bố cục

### 4.1. Thang khoảng cách (nền 4px)

`4 · 8 · 12 · 16 · 20 · 24 · 32 · 40 · 56 · 80`. Quy ước thường dùng:

| Vị trí | Giá trị |
|---|---|
| Nhãn → ô nhập | 8 |
| Giữa hai ô nhập | 18–24 |
| Giữa tiêu đề và đoạn mô tả | 8–12 |
| Giữa mô tả và biểu mẫu | 28–32 |
| Biểu mẫu → nút chính | 24 |
| Giữa hai mục cài đặt | 32 + đường kẻ |

### 4.2. Ba khung bố cục

| Khung | Cấu trúc | Dùng cho |
|---|---|---|
| **Xác thực (chia đôi)** | Bảng thương hiệu `ink` rộng 520 bên trái; cột biểu mẫu 400 bên phải trên nền `mist`, căn trái; dưới 1024px bỏ bảng thương hiệu, còn một cột | M1: đăng nhập, đăng ký, quên mật khẩu |
| **Ứng dụng** | Thanh bên 248 nền `paper`, đường kẻ phải; vùng nội dung nền `mist` | M2 chat, M4 tài liệu, cài đặt, quản trị |
| **Cài đặt (hai cột)** | Nội dung rộng 760: cột nhãn 240 bên trái (tiêu đề mục + mô tả), cột biểu mẫu 460 bên phải; các mục cách nhau bằng đường kẻ, không dùng thẻ | M1 cài đặt, M3 cấu hình, M4 kho tài liệu (phần thiết lập) |

Chiều rộng nội dung: biểu mẫu 400–460; danh sách và bảng tối đa 760; vùng chat tối đa 768 và căn giữa.

### 4.3. Responsive

- Điểm ngắt theo Tailwind (`sm 640`, `md 768`, `lg 1024`).
- Dưới `lg`: thanh bên thành ngăn kéo (Sheet), cài đặt chuyển thành một cột (nhãn nằm trên biểu mẫu).
- Khung chat toàn màn hình dùng `h-dvh`; vùng chạm tối thiểu 44×44.
- Dùng `@container` cho các thành phần có thể đặt trong ngăn bên hay ngăn kéo.

---

## 5. Hình khối

| Thành phần | Bán kính | Viền | Bóng |
|---|:---:|---|---|
| Chip, checkbox | 6 (`rounded-sm`) | 1 `input-line` | không |
| Nút, ô nhập, banner | 8 (`rounded-md`) | 1 `input-line` (ô nhập, nút viền) | không |
| Danh sách, vùng chứa, bảng | 12 (`rounded-lg`) | 1 `line` | không |
| Hộp thoại, bong bóng chat, ô soạn tin | 16 (`rounded-xl`) | không hoặc 1 `line` | `shadow-overlay` chỉ cho hộp thoại và menu |
| Avatar, huy hiệu, chip trích dẫn | tròn (`rounded-full`) | tùy loại | không |

Quy tắc:

- **Một lớp bề mặt:** `paper` đặt trực tiếp trên `mist`; không lồng thêm thẻ bên trong. Chia nhóm bằng khoảng trắng và đường kẻ `line`.
- **Bóng đổ** chỉ có một kiểu (`shadow-overlay`), dành cho lớp nằm trên nội dung (hộp thoại, menu, popover). Lớp phủ nền hộp thoại là `ink` độ mờ 50%.
- **Focus:** viền `blue` 2px kèm quầng 3px `blue` 25%.
- Đường phân cách giữa các hàng danh sách thụt vào 20 hai bên.

---

## 6. Biểu tượng

- Thư viện **Lucide** (`lucide-vue-next`), nét 2 (1.8 trong ô nhập), đầu và nối tròn.
- Cỡ: 16 (trong chữ, banner nhỏ), 20 (mặc định, điều hướng), 24 (tiêu đề, nút lớn), 64 (vòng tròn trạng thái trên màn hình thông báo, biểu tượng 28).
- Màu mặc định kế thừa chữ (`currentColor`); biểu tượng trạng thái dùng màu ngữ nghĩa.
- Nút chỉ có biểu tượng bắt buộc có `aria-label` và tooltip.
- Biểu tượng đã dùng ở M1: `mail`, `check`, `circle-alert`, `eye`, `eye-off`, `monitor`, `smartphone`, `trash-2`, `lock`, `log-out`, `arrow-left`, `shield`, `message-square`, `file-text`, `user`, `clock`, `x`, `arrow-up`.

---

## 7. Thành phần

Chiều cao điều khiển thống nhất: **ô nhập 44**, nút lớn **46** (nút gửi biểu mẫu xác thực), nút mặc định **44**, nút nhỏ **40**. Trên di động chiều cao không nhỏ hơn 44 cho phần tử chạm.

### 7.1. Nút

| Biến thể | Nền / viền / chữ | Dùng khi |
|---|---|---|
| Primary | nền `blue`, chữ trắng | Một hành động chính mỗi vùng |
| Secondary | nền `paper`, viền `input-line`, chữ `ink` | Hành động phụ, "Hủy", "Gửi lại" |
| Danger | nền `danger`, chữ trắng | Xác nhận hành động hủy hoại trong hộp thoại |
| Danger outline | nền `paper`, viền `danger-line` 1.5, chữ `danger` | Điểm vào của hành động hủy hoại (chưa xác nhận) |
| Ghost | không nền, chữ `ink`/`blue` | Hành động trong hàng, thanh công cụ |
| Disabled | nền `#E3E8EE`, chữ `#66728A` | Đang chờ (kèm lý do, ví dụ đếm ngược) |

- Nhãn là **động từ + đối tượng**, sentence case: "Tạo tài khoản", "Cập nhật mật khẩu".
- Nút đang xử lý giữ nguyên chiều rộng, hiện vòng xoay 16 và đổi nhãn sang dạng tiếp diễn ("Đang đăng nhập").
- Không dùng mũi tên cuối nhãn. Biểu tượng (nếu có) đặt bên trái.
- Một nhóm nút: hành động chính ở bên phải trong hộp thoại, ở trên cùng khi xếp dọc trên di động.

### 7.2. Ô nhập

| Trạng thái | Quy cách |
|---|---|
| Mặc định | nền `paper`, viền `input-line` 1, chữ `ink`, gợi ý `placeholder` |
| Focus | viền `blue` 2 + quầng 3px `blue` 25% |
| Lỗi | viền `danger` 1.5, biểu tượng + thông báo `danger` dưới ô (hoặc banner chung nếu lỗi không thuộc một ô) |
| Vô hiệu | nền `#E9EDF2`, viền `line` |

- Nhãn **luôn hiện phía trên** ô (13/600), không dùng placeholder thay nhãn.
- Trường mật khẩu có nút hiện/ẩn bên phải, `autocomplete` đúng (`current-password`, `new-password`, `email`).
- Văn bản trợ giúp 13/400 `muted-foreground`, nằm dưới ô.
- Ô lỗi không nêu thông tin gợi ý cho kẻ tấn công (xem M1, mục 7.3).

### 7.3. Checkbox, công tắc, radio

Checkbox 20×20, bán kính 6, đã chọn nền `blue` dấu tích trắng. Nhãn cùng dòng, chữ 13/400, vùng chạm bao cả nhãn. Công tắc (Switch) dùng cho tùy chọn có hiệu lực ngay; checkbox cho tùy chọn gửi cùng biểu mẫu.

### 7.4. Huy hiệu (Badge)

Cao 22, bo tròn, chữ 12/600, nền tint + chữ cùng màu ngữ nghĩa.

| Loại | Ví dụ |
|---|---|
| success | Đã xác thực, Thiết bị này, Thành công |
| info (`blue-tint`) | Đang xử lý, Mới |
| warning | Chờ xử lý, Sắp hết hạn |
| danger | Lỗi, Đã khóa |

### 7.5. Banner thông báo (Alert)

Nền tint, viền cùng họ màu, bán kính 8, biểu tượng 20 bên trái, **tiêu đề 14/600** một câu và mô tả 13/400 (nếu cần). Banner dùng cho kết quả của một hành động trên trang; không dùng cho chú thích chung.

| Loại | Biểu tượng |
|---|---|
| error | `circle-alert` |
| warning | `clock` hoặc `triangle-alert` |
| info | `shield` hoặc `info` |
| success | `check` |

### 7.6. Hộp thoại (Dialog)

Rộng 520 (tối đa `calc(100vw - 32px)`), bán kính 16, đệm 28, nền `paper`, `shadow-overlay`, lớp phủ `ink` 50%. Bố cục: biểu tượng trong vòng tròn 44 (tint ngữ nghĩa), tiêu đề 21/700 (câu hỏi cụ thể), mô tả nêu **hậu quả và thời điểm**, trường xác nhận nếu cần, hàng nút ở dưới cùng căn phải ("Hủy" bên trái, hành động bên phải). Có nút đóng `x` ở góc và đóng được bằng `Esc`.

### 7.7. Tab

Chữ 15; tab đang chọn đậm 700 trên **vệt highlight** (cao 19, bo 4) ở phần dưới chữ; các tab còn lại 500 `muted-foreground`. Đường kẻ `line` chạy dưới cả hàng. Khoảng cách giữa tab 32.

### 7.8. Điều hướng thanh bên

Rộng 248. Logo ở đầu; mục điều hướng cao 40, bán kính 8, biểu tượng 20 + chữ 15. Đang chọn: nền `blue-tint`, chữ và biểu tượng `blue`, đậm 600. Cuối thanh bên: avatar tròn 40 nền `ink` chữ trắng viết tắt, tên 14/600, email 11/400, nút đăng xuất.

### 7.9. Danh sách hàng (list row)

Vùng chứa `paper`, bán kính 12, viền `line`; mỗi hàng cao 88 (hàng đầy đủ) hoặc 56 (hàng gọn), phân cách bằng đường kẻ thụt 20. Hàng gồm: biểu tượng trong ô 40 (nền `mist`, bán kính 10), tiêu đề 15/600, dòng phụ 13/400, huy hiệu cạnh tiêu đề, hành động ở bên phải.

### 7.10. Bảng dữ liệu (quản trị, danh sách tài liệu)

Vùng chứa như 7.9; hàng tiêu đề nền `mist`, chữ 13/600; hàng dữ liệu cao 48, chữ 14; số căn phải, `tabular-nums`; chọn sắp xếp bằng biểu tượng mũi tên cạnh tên cột; phân trang ở dưới. Dưới `md` chuyển thành danh sách hàng.

### 7.11. Thông báo nhanh (Toast)

Dùng Sonner, góc dưới phải (trên di động: dưới giữa). Một dòng, 3–5 giây; không dùng cho lỗi cần hành động (dùng banner). Văn bản cùng động từ với nút: bấm "Lưu thay đổi" → toast "Đã lưu thay đổi".

### 7.12. Trạng thái rỗng, đang tải

- **Rỗng:** biểu tượng 64 trong vòng tròn tint, một câu nói điều gì sẽ xuất hiện ở đây, một nút hành động ("Tải tài liệu lên").
- **Đang tải:** khung xương (Skeleton) giữ đúng kích thước nội dung; vòng xoay 16 chỉ cho nút và thao tác ngắn.

---

## 8. Thành phần riêng của sản phẩm

Các thành phần này chưa xuất hiện ở M1 nhưng M2, M4, M5 sẽ cần; thiết kế sẵn để thống nhất.

### 8.1. Tin nhắn trong chat (M2)

| Thành phần | Quy cách |
|---|---|
| Tin nhắn người dùng | Căn phải, bong bóng `blue-tint`, bán kính 16, chữ `ink` 15, tối đa 80% chiều rộng |
| Câu trả lời | **Không bong bóng**, căn trái trên nền `mist`, chữ 15/1.65, tối đa `68ch`; Markdown theo mục 8.5 |
| Con trỏ streaming | Khối 2×18 `blue` nhấp nháy ở cuối chữ; tắt khi `prefers-reduced-motion` |
| Hành động trên câu trả lời | Hàng biểu tượng 20 gọn: sao chép, tạo lại, 👍, 👎; hiện khi hover hoặc luôn hiện trên di động |
| Dừng tạo | Nút secondary có biểu tượng `square`, thay chỗ nút gửi khi đang stream |

### 8.2. Trích dẫn nguồn (M5), thành phần đặc trưng

- **Chip trích dẫn:** cao 32, bo tròn, viền `line`, nền `paper`; số thứ tự trong vòng tròn 16 nền `blue` chữ trắng; biểu tượng `file-text` 14; tên tệp rút gọn + "trang N" 12/400. Bấm vào mở bảng nguồn.
- **Đoạn trích:** khi hiển thị nội dung được trích, bọc bằng lớp `.mark-hl` (vệt highlight, chữ `ink`).
- **Chỉ số trong câu:** số mũ nhỏ trong vòng tròn 16 nền `blue-tint` chữ `blue`, bấm để cuộn tới chip.
- **Bảng nguồn:** ngăn bên phải (Sheet) liệt kê các đoạn được dùng, mỗi đoạn có tên tài liệu, trang, đoạn trích có `.mark-hl`.
- Khi không đủ thông tin: câu trả lời nói rõ "Không tìm thấy trong tài liệu của bạn" kèm gợi ý, không hiển thị chip.

### 8.3. Ô soạn tin (M2)

Vùng nhập bán kính 16, nền `paper`, viền `input-line`, rộng tối đa 768, căn giữa, dính đáy khung chat. Gồm ô nhập tự giãn (1 đến 8 dòng), công tắc chế độ (8.4) và nút gửi tròn 40 nền `blue` biểu tượng `arrow-up`. Nút gửi vô hiệu hóa khi ô trống. `Enter` gửi, `Shift+Enter` xuống dòng. Hiển thị phạm vi tài liệu đang dùng ngay trong ô khi ở chế độ theo tài liệu.

### 8.4. Công tắc chế độ chat (M2)

Hai lựa chọn dạng chip: **Chat thông thường** và **Theo tài liệu**. Chip đang chọn có nền `blue-tint`, chữ `blue`; chip "Theo tài liệu" kèm biểu tượng `file-text` và số tài liệu trong phạm vi. Chế độ hiện tại luôn nhìn thấy trong ô soạn tin.

### 8.5. Nội dung Markdown trong câu trả lời

| Phần tử | Quy cách |
|---|---|
| Tiêu đề trong câu trả lời | 17/700 (h1–h3 cùng cỡ để không lấn tiêu đề trang) |
| Danh sách | Giãn dòng 6, dấu chấm `muted-foreground` |
| Mã dòng | JetBrains Mono 13, nền `#E3E8EE`, bo 6, đệm 2×6 |
| Khối mã | Nền `ink`, chữ `#E6ECF5`, bo 12, đệm 16, nút sao chép góc phải trên |
| Bảng | Như 7.10, cuộn ngang trong khung riêng |
| Trích dẫn (blockquote) | Đường kẻ trái 3 `line`, chữ `muted-foreground` |

### 8.6. Tải tài liệu (M4)

- **Vùng thả tệp:** viền nét đứt `input-line` 1.5, bán kính 12, nền `paper`; biểu tượng `upload` 24, câu "Kéo thả tệp vào đây hoặc chọn từ máy", dòng phụ nêu định dạng và dung lượng tối đa. Khi kéo qua: nền `blue-tint`, viền `blue`.
- **Trạng thái nạp:** huy hiệu — Đang xử lý (info, có vòng xoay), Sẵn sàng (success), Lỗi (danger, kèm "Thử lại").
- **Hạn mức:** thanh tiến độ cao 6, bo tròn; dưới 80% nền `blue`, từ 80% `warning`, đạt 100% `danger`; chữ "12 / 50 tệp · 48 / 200 MB" dạng `tabular-nums`.

---

## 9. Mẫu màn hình theo nghiệp vụ

| Nghiệp vụ | Màn hình cần thiết kế | Khung | Gợi ý dùng lại |
|---|---|---|---|
| M1 | Đã xong (14 màn hình) | Xác thực, Ứng dụng | |
| M2 Chat | Khung chat, danh sách hội thoại, tìm kiếm hội thoại, trạng thái rỗng, lỗi gửi/hết quota | Ứng dụng (thanh bên là danh sách hội thoại) | 8.1, 8.3, 8.4, 7.9, 7.12 |
| M3 Cấu hình mặc định | Prompt riêng, mô hình, phạm vi tài liệu; ghi đè theo hội thoại | Cài đặt hai cột | 7.2, 7.3, 4.2; ghi đè trong hội thoại dùng Sheet bên phải |
| M4 Kho tài liệu | Danh sách tài liệu, tải lên, trạng thái nạp, nhóm tài liệu, xóa | Ứng dụng | 7.10, 8.6, 7.4, 7.6 |
| M5 RAG | Chip và bảng nguồn, đoạn trích, trạng thái "không tìm thấy" | Nằm trong khung chat | 8.2 |
| M6 Bảo mật | Mức sử dụng và quota của người dùng, yêu cầu xóa dữ liệu | Cài đặt hai cột | 8.6 (thanh hạn mức), 7.6 |
| M7 Quản trị | Danh sách người dùng, chi tiết và khóa, mức sử dụng, chi phí | Ứng dụng với thanh bên quản trị | 7.10, 7.4, 7.6; biểu đồ dùng bảng màu 9.1 |
| M8 Phản hồi và giám sát | Nhật ký hội thoại, thống kê feedback, đánh giá RAG | Ứng dụng | 7.10, 8.2 |

### 9.1. Màu biểu đồ (M7, M8)

Dùng tối đa 4 chuỗi: `blue` `#2B3CE6`, `ink` `#0F1B2D`, `#7A86F0` (blue sáng), `#8A95A6` (xám). Dữ liệu có ý nghĩa đúng/sai dùng `success`/`danger`. Luôn kèm nhãn hoặc kiểu nét khác nhau, không chỉ dựa vào màu. Không dùng highlight cho biểu đồ.

---

## 10. Cách viết nội dung

### 10.1. Giọng điệu

Ngắn, rõ, trung tính, xưng "bạn", dùng động từ chủ động. Lỗi nêu điều đã xảy ra và cách khắc phục, **không xin lỗi vòng vo**, không đổ lỗi cho người dùng.

| Tình huống | Nên | Không nên |
|---|---|---|
| Nút | "Lưu thay đổi" | "Gửi", "OK" |
| Lỗi | "Không tải được tệp. Kiểm tra kết nối rồi thử lại." | "Rất tiếc! Đã có lỗi xảy ra." |
| Rỗng | "Chưa có tài liệu. Tải lên tệp đầu tiên để bắt đầu hỏi đáp." | "Không có dữ liệu" |
| Xác nhận hủy hoại | "Xóa 3 tài liệu? Các đoạn đã nạp cũng bị xóa." / nút "Xóa 3 tài liệu" | "Bạn có chắc không?" / nút "Có" |

### 10.2. Từ vựng thống nhất

| Dùng | Không dùng |
|---|---|
| Đăng nhập, Đăng xuất, Đăng ký | Thoát, Login |
| Tài khoản | Account, Hồ sơ cá nhân (trừ tab Hồ sơ) |
| Hội thoại | Cuộc trò chuyện, Chat (khi chỉ danh từ) |
| Trò chuyện | Chat (khi là mục điều hướng) |
| Tài liệu, tệp (từng tệp tải lên) | File, văn bản |
| Nguồn / trích dẫn | Tham chiếu, reference |
| Xóa (hủy hoại, không hoàn tác) | Gỡ, Loại bỏ |
| Hủy (bỏ thao tác đang làm) | Đóng (chỉ để đóng cửa sổ) |
| Thiết bị, phiên đăng nhập | Session |
| Mô hình | Model |
| Lượt tin nhắn, token | Request |

### 10.3. Quy ước định dạng

- Ngày: `11/10/2026`; giờ 24 giờ `14:32`; thời gian tương đối khi dưới 7 ngày ("2 giờ trước").
- Dung lượng: `48 MB` (khoảng trắng trước đơn vị); số lớn có dấu chấm ngăn nghìn theo tiếng Việt (`1.250`).
- Tên tệp dài rút gọn ở giữa, giữ đuôi (`hop-dong-…-2026.pdf`).
- Không dùng dấu chấm than, emoji trang trí; emoji 👍/👎 chỉ ở nút phản hồi.

---

## 11. Trạng thái và phản hồi

| Tình huống | Cách thể hiện |
|---|---|
| Thao tác ngắn (lưu, sao chép) | Toast một dòng |
| Lỗi cần người dùng sửa | Banner trong trang (có tiêu đề và hướng xử lý) |
| Lỗi ô nhập định dạng | Thông báo dưới ô, biểu tượng + chữ `danger` |
| Hành động hủy hoại | Hộp thoại nêu hậu quả, nút đặt tên đúng hành động; xác nhận bằng mật khẩu khi ảnh hưởng tài khoản |
| Đang chờ có thời hạn | Đếm ngược trong nút vô hiệu ("Gửi lại sau 0:42") |
| Hết quota, bị giới hạn tốc độ | Banner `warning` nêu thời điểm dùng lại hoặc cách tăng hạn mức |
| Mất kết nối | Banner cố định phía trên nội dung, tự biến mất khi có mạng |
| Phiên hết hạn | Chuyển về đăng nhập, giữ đường dẫn để quay lại, nêu lý do bằng banner `info` |

---

## 12. Chuyển động

- Mặc định **không có chuyển động tự phát**; không hiệu ứng trượt vào khi tải trang.
- Chuyển động đáp lại thao tác: đổi màu nút và viền 120ms, mở hộp thoại hoặc ngăn kéo 180ms với `ease-out`, mở rộng vùng gập 180ms.
- Một ngoại lệ có chủ đích: con trỏ streaming khi chatbot trả lời và vòng xoay khi chờ.
- Tôn trọng `prefers-reduced-motion` (đã đặt trong `tokens.css`).

---

## 13. Trợ năng

- Tương phản: đạt AA theo bảng 2.2; viền điều khiển đạt 3:1.
- Vòng focus nhìn thấy trên mọi phần tử tương tác; thứ tự Tab theo thứ tự đọc.
- Nhãn ô nhập gắn với ô bằng `for`/`id`; lỗi gắn bằng `aria-describedby`; banner lỗi dùng `role="alert"` hoặc `aria-live="polite"`.
- Câu trả lời đang stream: vùng `aria-live="polite"` cập nhật theo câu, không đọc từng ký tự.
- Vùng chạm tối thiểu 44×44 trên di động; khoảng cách giữa hai vùng chạm ít nhất 8.
- Không truyền đạt thông tin chỉ bằng màu; biểu tượng đứng riêng có `aria-label`.
- Hộp thoại giữ focus bên trong, trả focus về nút mở khi đóng (Reka UI đã hỗ trợ).
- Đặt `lang="vi"` trên thẻ `html`.

---

## 14. Dùng trong code

### 14.1. Cài đặt

1. Cài font: `npm i @fontsource-variable/inter @fontsource-variable/jetbrains-mono`.
2. Trong `main.css`: `@import "tailwindcss"; @import "./tokens.css";`.
3. Cài các thành phần shadcn-vue cần dùng (`button`, `input`, `label`, `checkbox`, `alert`, `dialog`, `tabs`, `badge`, `sonner`, `sheet`, `skeleton`, `table`).
4. Các biến `--primary`, `--border`, `--input`, `--ring`... đã ánh xạ nên thành phần shadcn-vue tự nhận đúng màu.

### 14.2. Điều chỉnh nút của shadcn-vue

Đặt lại biến thể trong `components/ui/button/index.ts`:

```ts
import { cva } from 'class-variance-authority'

export const buttonVariants = cva(
  'inline-flex items-center justify-center gap-2 rounded-md font-semibold text-body transition-colors ' +
  'duration-[120ms] focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-ring ' +
  'disabled:pointer-events-none disabled:bg-muted disabled:text-[#66728A] [&_svg]:size-4 [&_svg]:shrink-0',
  {
    variants: {
      variant: {
        default: 'bg-primary text-primary-foreground hover:bg-primary/90',
        secondary: 'bg-card text-foreground border border-input hover:bg-muted/60',
        destructive: 'bg-destructive text-destructive-foreground hover:bg-destructive/90',
        'destructive-outline': 'bg-card text-destructive border-[1.5px] border-danger-line hover:bg-danger-tint',
        ghost: 'text-foreground hover:bg-muted/60',
        link: 'text-primary font-medium underline-offset-4 hover:underline',
      },
      size: {
        lg: 'h-[46px] px-5',
        default: 'h-11 px-5',
        sm: 'h-10 px-4 text-small',
        icon: 'size-11',
      },
    },
    defaultVariants: { variant: 'default', size: 'default' },
  },
)
```

### 14.3. Ô nhập, tab, badge

```ts
// Input: cao 44, viền input, focus theo tokens.css
'h-11 w-full rounded-md border border-input bg-card px-3.5 text-body placeholder:text-[#6B778A] ' +
'disabled:bg-[#E9EDF2] disabled:border-border aria-[invalid=true]:border-destructive aria-[invalid=true]:border-[1.5px]'

// Tab đang chọn: vệt highlight
'data-[state=active]:font-bold data-[state=active]:text-foreground ' +
'data-[state=active]:[background:linear-gradient(transparent_14%,var(--highlight)_14%,var(--highlight)_92%,transparent_92%)]'

// Badge thành công
'inline-flex h-[22px] items-center rounded-full bg-success-tint px-2.5 text-xs font-semibold text-success'
```

### 14.4. Cấu trúc thư mục gợi ý

```
src/
  styles/
    main.css          // @import tailwindcss + tokens.css
    tokens.css
  components/
    ui/               // shadcn-vue (đã chỉnh theo mục 14.2–14.3)
    app/              // thành phần dùng chung của dự án
      PasswordInput.vue
      PasswordStrength.vue
      CitationChip.vue
      SourceQuote.vue        // dùng .mark-hl
      EmptyState.vue
      ConfirmDialog.vue      // mẫu hộp thoại hủy hoại (7.6)
      SettingsSection.vue    // bố cục hai cột (4.2)
  layouts/
    AuthLayout.vue    // chia đôi
    AppLayout.vue     // thanh bên + nội dung
```

---

## 15. Danh sách kiểm tra cho màn hình mới

- [ ] Dùng một trong ba khung bố cục ở 4.2.
- [ ] Chỉ có **một** nút primary mỗi vùng; hành động hủy hoại dùng danger outline rồi mới đến hộp thoại.
- [ ] Không có thẻ lồng thẻ; bóng đổ chỉ ở lớp phủ.
- [ ] Màu chỉ lấy từ bảng 2.1; highlight chỉ dùng đúng chỗ theo 2.3.
- [ ] Nhãn ô nhập luôn hiện; lỗi có biểu tượng và chữ.
- [ ] Nội dung sentence case, nút là động từ, từ vựng theo 10.2.
- [ ] Có đủ trạng thái: mặc định, đang tải, rỗng, lỗi, thành công.
- [ ] Đã thiết kế bản di động (một cột, vùng chạm 44).
- [ ] Đã kiểm tra tương phản nếu thêm màu hoặc cặp chữ/nền mới.
- [ ] Thành phần mới được thêm vào tài liệu này (mục 7 hoặc 8) để màn hình sau dùng lại.
