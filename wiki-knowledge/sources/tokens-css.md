---
title: tokens.css (nguồn)
date: 2026-10-04
tags: [source, design, token]
sources: [docs/tokens.css]
---

# `tokens.css` — nguồn token

File CSS thật, import một lần trong `main.css` (`@import "tailwindcss"; @import "./tokens.css";`). Tương thích shadcn-vue (Reka UI) + Tailwind CSS v4.

Gồm bốn phần: bảng màu gốc → ánh xạ biến shadcn-vue → khối `@theme inline` (biến Tailwind + cỡ chữ + bán kính + bóng) → `@layer base/components` (focus ring, `prefers-reduced-motion`, class `.mark-hl`, `.tabular`).

Giá trị đã biên soạn sang [[design-tokens]]; quy tắc dùng màu/chữ ở [[quy-tac-thiet-ke]].

> Font cài qua `@fontsource-variable/inter` và `@fontsource-variable/jetbrains-mono` để không phụ thuộc CDN.
