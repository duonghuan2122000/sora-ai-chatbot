---
title: Điểm còn mở
date: 2026-10-04
tags: [quyet-dinh, mo]
sources: [docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md, docs/auth/m1-tai-khoan-va-xac-thuc.md]
---

# Điểm còn mở

Các câu hỏi **chưa chốt**, gộp từ v2 §8 và M1 §14. Không tự quyết thay người dùng.

## Quy mô và hạ tầng

- [ ] **Quy mô dự kiến**: số người dùng, số tài liệu mỗi người → chốt phương án vector search ([[vector-search-mariadb]]). Đây là câu chặn nhiều quyết định khác.
- [ ] **Vị trí triển khai SPA và API**: cùng site hay khác site → quyết định `SameSite` và CORS ([[xac-thuc-va-token]], [[bao-mat-ung-dung]]).

## Sản phẩm

- [ ] **Chính sách đăng ký**: mở tự do hay theo lời mời? Cờ `registration_mode` đã chừa, nhưng nếu mở tự do thì giai đoạn B và C của M1 ([[m1-tai-khoan-va-xac-thuc]]) là **bắt buộc**.
- [ ] **Chặn tài khoản chưa xác thực đến mức nào**: chặn hoàn toàn chat + tải tài liệu (đề xuất) hay cho dùng thử vài tin nhắn?
- [ ] **Phạm vi "cấu hình mặc định" (M3)**: chỉ prompt hướng dẫn, hay gồm cả mô hình, nhiệt độ, phạm vi tài liệu?
- [ ] **Phạm vi tài liệu theo hội thoại**: người dùng chọn tài liệu nào cho từng hội thoại, hay áp dụng toàn bộ kho?
- [ ] **Nhiều mô hình LLM cho người dùng chọn, hay chỉ một?**
- [ ] **Thu phí / chia gói dịch vụ?** → ảnh hưởng #17 và thiết kế quota.
- [ ] **Đăng nhập Google/GitHub**: làm ngay hay sau MVP?

## Nhà cung cấp bên ngoài

- [ ] **Nhà cung cấp LLM** (đã chốt "qua API", chưa chốt ai).
- [ ] **Nhà cung cấp email** và tên miền gửi ([[bao-mat-ung-dung]]).
- [ ] **CAPTCHA**: Turnstile hay hCaptcha; có chấp nhận phụ thuộc dịch vụ ngoài không ([[mat-khau-va-chong-lam-dung]]).
- [ ] **Embedding/reranker**: BGE-M3 hay multilingual-e5; reranker `bge-reranker-v2-m3` ([[ngan-xep-ky-thuat]]).

## Chính sách dữ liệu

- [ ] **Thời gian chờ xóa tài khoản** (đề xuất 7 ngày) và **thời hạn giữ audit log ẩn danh**.
- [ ] **Chính sách dữ liệu gửi ra LLM bên ngoài** (mức 2, #11).
- [ ] **Rà soát pháp lý** theo Nghị định 13/2023/NĐ-CP trước khi mở công khai.
