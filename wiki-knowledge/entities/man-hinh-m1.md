---
title: Màn hình M1 (14 màn hình)
date: 2026-10-04
tags: [m1, design, man-hinh]
sources: [docs/auth/01-dang-nhap.svg, docs/auth/m1-tai-khoan-va-xac-thuc.md, docs/design-system.md]
---

# Màn hình M1 — 14 file SVG

Nguồn thị giác để dựng UI M1, nằm trong `docs/auth/`. Desktop 1280×800, di động 390×844. Tên sản phẩm **Sora AI Chatbot**, font **Inter**. Quy tắc dựng: [[quy-tac-thiet-ke]].

| File | Màn hình | Trạng thái |
|---|---|---|
| `01-dang-nhap.svg` | Đăng nhập | Mặc định, ô mật khẩu đang focus |
| `02-dang-nhap-loi.svg` | Đăng nhập sai | Banner lỗi chung; hiện CAPTCHA sau 3 lần sai |
| `03-dang-nhap-tam-khoa.svg` | Khóa tạm | Đếm ngược 15 phút, nút vô hiệu |
| `04-dang-ky.svg` | Đăng ký | Thanh độ mạnh mật khẩu, điều khoản, CAPTCHA đã xác minh |
| `05-kiem-tra-hop-thu.svg` | Kiểm tra hộp thư | Gửi lại có đếm ngược |
| `06-xac-thuc-thanh-cong.svg` | Xác thực thành công | |
| `07-xac-thuc-het-han.svg` | Liên kết hết hạn | Cho nhận liên kết mới |
| `08-quen-mat-khau.svg` | Quên mật khẩu | |
| `09-quen-mat-khau-da-gui.svg` | Đã gửi liên kết | Nội dung trung tính, không lộ email tồn tại |
| `10-dat-lai-mat-khau.svg` | Đặt mật khẩu mới | Nhắc sẽ đăng xuất mọi thiết bị |
| `11-cai-dat-bao-mat.svg` | Cài đặt — tab Bảo mật | Đổi mật khẩu, trạng thái email |
| `12-cai-dat-phien-dang-nhap.svg` | Cài đặt — tab Phiên đăng nhập | Thu hồi từng thiết bị hoặc tất cả thiết bị khác |
| `13-xoa-tai-khoan.svg` | Hộp thoại xóa tài khoản | Xác nhận bằng mật khẩu, nêu rõ hậu quả |
| `14-dang-nhap-di-dong.svg` | Đăng nhập trên di động | Bố cục một cột |

## Định hướng thị giác

Nền Mist `#F3F5F8` / Paper `#FFFFFF` · chủ đạo Ink `#0F1B2D` (bảng thương hiệu) + Ultramarine `#2B3CE6` (hành động) · điểm nhấn Highlighter `#FFE66D` (tab đang chọn, biểu tượng) · trạng thái Danger `#B8312A`, Success `#17784F`, cảnh báo hổ phách. Giá trị token: [[design-tokens]].

## Ghi chú

Bộ màn hình này là **nguồn rút ra [[quy-tac-thiet-ke]]** — nó có trước design system, nên khi mâu thuẫn giữa SVG và design system thì theo **design system** (đã chỉnh màu để đạt tương phản).
