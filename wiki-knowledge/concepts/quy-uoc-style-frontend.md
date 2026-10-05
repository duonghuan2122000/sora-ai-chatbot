---
title: Quy ước style frontend (SCSS)
date: 2026-10-05
tags: [frontend, scss, quy-uoc]
sources: [docs/quy-uoc-code.md]
---

# Quy ước style frontend

Biên soạn từ [[quy-uoc-code]] (`docs/quy-uoc-code.md`), bổ sung 2026-10-05.

## Luật

1. **Luôn dùng SCSS**, không viết CSS thuần cho style do mình viết.
2. **Dùng nested SCSS** — lồng selector theo cấu trúc BEM ở [[quy-uoc-dat-ten]], không lặp lại tên block.
3. **Có `base.scss`** định nghĩa biến CSS dùng chung cho toàn frontend (kế thừa giá trị từ [[design-tokens]] / `docs/tokens.css`).

## Ví dụ

```scss
// base.scss — biến dùng chung
:root {
  --sora-radius-chip: 6px;
  --sora-radius-control: 8px;
  --sora-space-3: 12px;
}
```

```scss
// CitationChip.vue — <style lang="scss" scoped>
.sora-citation-chip {
  border-radius: var(--sora-radius-chip);

  &__label {
    padding-inline: var(--sora-space-3);
  }

  &--active {
    text-decoration: underline;
  }
}
```

## Ghép với Tailwind (đã chốt 2026-10-05)

Tailwind v4 giữ **entry CSS thuần** đúng như `docs/design-system.md` §14: `main.css` với `@import "tailwindcss"; @import "./tokens.css";`.

`main.ts` import **hai file song song**, độc lập:

```ts
import './styles/main.css'   // Tailwind v4 + tokens
import './styles/base.scss'  // biến :root dùng chung
```

Không gộp `@import "tailwindcss"` vào file SCSS — Tailwind v4 chạy qua Vite plugin và dễ vỡ khi đi qua bước biên dịch Sass. Chỉ **style thành phần** mới dùng `<style lang="scss" scoped>`.
