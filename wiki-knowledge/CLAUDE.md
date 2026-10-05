# Wiki Sora AI Chatbot — schema

Wiki này là **LLM-wiki**: kiến thức được biên soạn một lần từ tài liệu thô, rồi giữ mới dần, thay vì truy xuất lại từ đầu mỗi lần hỏi. LLM sở hữu toàn bộ tầng wiki; người dùng chọn nguồn và định hướng phân tích.

## Ba tầng

| Tầng | Ở đâu | Quy tắc |
|---|---|---|
| Raw sources | `../docs/` (repo, không copy vào đây) | **Bất biến** — chỉ đọc, không sửa |
| Wiki | thư mục này | LLM tạo và bảo trì toàn bộ |
| Schema | file này | Cấu hình cho domain này |

Nguồn thô nằm ở `docs/` của repo chứ không nhân bản vào `raw/`: tránh hai bản lệch nhau, và `docs/` đã được git version hóa.

## Cấu trúc

```
wiki-knowledge/
  CLAUDE.md      schema (file này)
  index.md       catalog mọi page, một dòng mỗi page
  log.md         nhật ký append-only, mỗi thao tác một dòng
  sources/       một page tóm tắt cho mỗi tài liệu nguồn
  concepts/      một page cho mỗi khái niệm / quyết định kỹ thuật
  entities/      module nghiệp vụ (M1–M8) và màn hình
  decisions/     điểm còn mở, thay đổi giữa các phiên bản tài liệu
```

## Quy ước page

- Markdown + frontmatter YAML: `title`, `date` (YYYY-MM-DD, ngày biên soạn/cập nhật), `tags`, `sources` (đường dẫn tương đối trong `docs/`).
- Mỗi page **một** chủ đề; không nhồi nhiều chủ đề vào một file.
- Liên kết chéo bằng `[[tên-file-không-đuôi]]`.
- Ngôn ngữ: **tiếng Việt**, theo từ vựng ở `docs/design-system.md` mục 10.2 (Hội thoại, Tài liệu, Nguồn — không dùng Chat/File/reference).
- Ghi rõ khi một claim bị nguồn mới hơn phủ định: dòng `> ⚠️ **Mâu thuẫn:**` kèm nguồn nào thắng.
- Không chép nguyên khối dài từ nguồn; tóm tắt + trỏ về mục cụ thể của nguồn.

## Thao tác

**Ingest** (nguồn mới trong `docs/`): đọc nguồn → viết `sources/<slug>.md` → cập nhật `index.md` → sửa mọi page liên quan (thêm cross-reference, đánh dấu mâu thuẫn) → append `log.md` → báo user danh sách page đã đụng.

**Query**: đọc `index.md` trước, chọn page liên quan, đọc sâu page đó — **không** quét lại `docs/`. Trả lời kèm citation dạng `[[page]]`. Nếu câu trả lời có giá trị lâu dài → đề xuất lưu thành page mới.

**Lint**: tìm mâu thuẫn giữa các page, claim lỗi thời, orphan page, khái niệm quan trọng chưa có page, thiếu cross-reference, lỗ hổng dữ liệu.

Định dạng log: `## [YYYY-MM-DD] <thao tác> | <mô tả>` với thao tác ∈ `ingest | update | query | lint`.

## Bản đồ nguồn → page

| Nguồn | Page tóm tắt | Trạng thái |
|---|---|---|
| `docs/ai-chatbot-tong-quan.md` (v1) | [[tong-quan-v1]] | Một phần đã bị v2 thay |
| `docs/ai-chatbot-nghiep-vu-va-uu-tien-v2.md` | [[nghiep-vu-uu-tien-v2]] | **Nguồn sự thật** khi mâu thuẫn |
| `docs/design-system.md` | [[design-system]] (nguồn) → [[quy-tac-thiet-ke]] | Bắt buộc cho mọi màn hình |
| `docs/tokens.css` | [[tokens-css]] (nguồn) → [[design-tokens]] | Token thật |
| `docs/auth/m1-tai-khoan-va-xac-thuc.md` | [[m1-spec]] | Đặc tả chi tiết M1 |
| `docs/auth/*.svg` (14 file) | [[man-hinh-m1]] | Nguồn thị giác M1 |
| `docs/quy-uoc-code.md` | [[quy-uoc-code]] | Quy ước code (đặt tên, SCSS, khóa ngoại) — **thắng** khi mâu thuẫn về phạm vi này |
