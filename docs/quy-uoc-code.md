# Quy ước code — Sora AI Chatbot

> Quy ước đặt tên, style và ràng buộc dữ liệu áp dụng cho toàn bộ backend, frontend và CSDL. Bổ sung 2026-10-05. Khi tài liệu khác mâu thuẫn với file này về các điểm dưới đây, file này thắng.

## 1. Backend — API

### 1.1. Tên trường trong request/response body

Dùng **`snake_case`** cho mọi trường JSON, cả request body và response body. Không camelCase, không PascalCase.

```json
{
  "email_normalized": "a@example.com",
  "terms_accepted_at": "2026-10-05T09:12:00.000Z",
  "expires_at": "2026-11-04T09:12:00.000Z"
}
```

Go struct vẫn theo tên export kiểu Go, nhưng **mọi** trường ra/vào JSON phải có tag tường minh:

```go
type LoginRequest struct {
    Email    string `json:"email"`
    Password string `json:"password"`
}
```

Tên trường khớp cột CSDL tương ứng (mục 3) — cùng một khái niệm thì cùng một tên ở mọi tầng.

### 1.2. Header tự đặt

Header do hệ thống tự định nghĩa mang **tiền tố `x-sora-`**, viết thường.

| Header | Dùng cho |
|---|---|
| `x-sora-request-id` | Mã vết yêu cầu, trả lại trong response để đối chiếu log |
| `x-sora-conversation-id` | Hội thoại đang xử lý (M2) |

Header chuẩn giữ nguyên tên gốc, **không** thêm tiền tố: `Authorization`, `Origin`, `X-Requested-With`, `Content-Type`, và các header bảo mật ở `docs/auth/m1-tai-khoan-va-xac-thuc.md` mục 7.

## 2. Frontend

### 2.1. Style

- **Luôn dùng SCSS**, không viết CSS thuần cho style do mình viết. Trong component Vue: `<style lang="scss" scoped>`.
- **Dùng nested SCSS** — lồng selector theo cấu trúc BEM, không lặp lại tên block.
- **Có `base.scss`** định nghĩa biến CSS dùng chung toàn frontend (giá trị lấy từ `docs/tokens.css`).

Tailwind v4 vẫn giữ entry CSS thuần theo `docs/design-system.md` mục 14: `main.ts` import **cả hai** file độc lập — `main.css` (`@import "tailwindcss"; @import "./tokens.css";`) và `base.scss`. Không gộp Tailwind vào file SCSS.

```scss
// base.scss
:root {
  --sora-radius-chip: 6px;
  --sora-space-3: 12px;
}
```

```scss
// CitationChip.vue
<style lang="scss" scoped>
.sora-citation-chip {
  border-radius: var(--sora-radius-chip);

  &__label { padding-inline: var(--sora-space-3); }
  &--active { text-decoration: underline; }
}
</style>
```

### 2.2. Tên class HTML

Theo **BEM**, có **tiền tố `sora-`**:

| Phần | Dạng | Ví dụ |
|---|---|---|
| Block | `.sora-<block>` | `.sora-citation-chip` |
| Element | `.sora-<block>__<element>` | `.sora-citation-chip__label` |
| Modifier | `.sora-<block>--<modifier>` | `.sora-citation-chip--active` |

Tiền tố tránh đụng class của shadcn-vue và thư viện ngoài. Class tiện ích của Tailwind vẫn dùng trực tiếp trong template; BEM áp cho class do mình viết trong SCSS, không thay thế Tailwind.

## 3. CSDL

### 3.1. Tên đối tượng

Bảng, tên cột, primary key, unique key, foreign key, index đều **`snake_case`**. Khóa có tiền tố theo loại:

| Loại | Tiền tố | Ví dụ |
|---|---|---|
| Primary key | — (tên cột `id`) | `id` |
| Unique key | `uq_` | `uq_users_email`, `uq_sessions_refresh` |
| Foreign key | `fk_` | `fk_sessions_user` |
| Index thường | `idx_` | `idx_sessions_user`, `idx_audit_event` |

Bảng đặt tên số nhiều, tiếng Anh: `users`, `sessions`, `auth_tokens`, `documents`, `chunks`.

### 3.2. Khóa ngoại — hạn chế khai báo

**Hạn chế tối đa việc khai `FOREIGN KEY`**, để dữ liệu linh hoạt: dễ tách/nhập bảng, dễ xóa mềm, dễ đổi kiểu khóa, không vướng thứ tự ghi.

**Ngoại lệ có chủ đích:** `sessions` và `auth_tokens` giữ `FOREIGN KEY ... ON DELETE CASCADE` tới `users` (xem `docs/auth/m1-tai-khoan-va-xac-thuc.md` mục 4.2 và 4.3). Hai bảng này nhỏ, vòng đời gắn chặt `users`, và cascade ở đây bảo đảm xóa tài khoản không để lại phiên đăng nhập mồ côi.

**Từ M4 trở đi (tài liệu, chunk, hội thoại, tin nhắn): không khai khóa ngoại.** Tầng ứng dụng lo:

- Vẫn tạo index trên cột tham chiếu (`user_id`, `document_id`) vì truy vấn luôn lọc theo `uid`.
- Xóa theo thứ tự tường minh trong một giao dịch, không dựa vào cascade của CSDL.
- Kiểm thử phải phủ: xóa tài khoản, xóa tài liệu kèm chunk/vector, và job dọn bản ghi mồ côi.
