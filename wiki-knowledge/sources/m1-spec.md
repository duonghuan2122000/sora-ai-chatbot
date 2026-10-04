---
title: Đặc tả M1 (nguồn)
date: 2026-10-04
tags: [source, m1, xac-thuc]
sources: [docs/auth/m1-tai-khoan-va-xac-thuc.md]
---

# Đặc tả chi tiết M1 — Tài khoản và xác thực

Tài liệu dài nhất về một module (39 KB), phiên bản 1.0. Gồm 14 mục: phạm vi, quyết định thiết kế, kiến trúc, mô hình dữ liệu (SQL đầy đủ), thiết kế API, luồng nghiệp vụ (có sơ đồ Mermaid), chính sách bảo mật, cấu trúc code Go, cấu trúc frontend Vue, danh sách màn hình, nội dung thông báo, kiểm thử, kế hoạch triển khai, điểm cần chốt.

Đã biên soạn thành:

- [[m1-tai-khoan-va-xac-thuc]] — phạm vi, API, luồng, kế hoạch triển khai
- [[man-hinh-m1]] — 14 màn hình và trạng thái
- [[xac-thuc-va-token]] — cơ chế token
- [[mat-khau-va-chong-lam-dung]] — argon2id, rate limit, CAPTCHA
- [[chong-do-email]] — chống dò email và dò thời gian
- [[mo-hinh-token-frontend]] — vòng đời token phía Vue
- [[thiet-ke-du-lieu-mariadb]] — schema bảng

## Ghi chú cho wiki

Đây là **hợp đồng thi công** cho M1: khi viết code Go/Vue cho module này, đối chiếu lại đúng mục trong nguồn — không suy đoán thêm. Cấu trúc thư mục Go ở §8.1 và Vue ở §9 + [[quy-tac-thiet-ke]] §14.4 là nguồn để dựng `app/backend/` và `app/frontend/` (hiện đang trống/scaffold rác).
