# Publishing Workflow

Tài liệu này chuẩn hóa quy trình xuất bản nội dung cho blog KevinSec, áp dụng cho `Blog`, `Research` và các page nội dung quan trọng.

## 1. Mục tiêu

Publishing workflow giúp đảm bảo mỗi bài viết khi xuất bản đều:

- Có metadata đầy đủ.
- Có URL/slug rõ ràng, ổn định.
- Có ảnh cover/thumbnail đúng chuẩn.
- Build Hugo không lỗi.
- Deploy thành công qua GitHub Actions.
- Hiển thị tốt trên desktop, mobile, search, RSS, sitemap và social preview.

## 2. Quy trình tổng quan

```text
Draft content
    ↓
Review metadata
    ↓
Check media
    ↓
Set draft = false
    ↓
Commit & push
    ↓
GitHub Actions build
    ↓
Verify live site
    ↓
Share / distribute
```

## 3. Trạng thái bài viết

### Draft

Dùng khi bài chưa sẵn sàng xuất bản.

```yaml
draft: true
```

### Published

Chỉ đổi sang `false` khi bài đã hoàn chỉnh nội dung, metadata và hình ảnh.

```yaml
draft: false
```

## 4. Front Matter Checklist

Trước khi publish, mỗi bài cần kiểm tra các trường sau:

```yaml
title: ""
slug: ""
summary: ""
description: ""
date: "YYYY-MM-DD"
lastmod: "YYYY-MM-DD"
draft: false
tags: []
categories: []
cover:
  image: "cover.webp"
  alt: ""
  caption: ""
  relative: true
toc: true
aliases: []
```

### Quy ước nhanh

- `title`: rõ nghĩa, có keyword chính.
- `slug`: ngắn, lowercase, dùng dấu `-`.
- `summary`: dùng cho danh sách bài viết.
- `description`: dùng cho SEO và social preview.
- `date`: ngày publish lần đầu.
- `lastmod`: ngày cập nhật gần nhất.
- `draft`: đặt `false` khi publish.
- `tags`: 3–6 tag liên quan.
- `categories`: nhóm nội dung chính, ví dụ `blog`, `research`, `web-security`.
- `cover.image`: dùng ảnh ngang, ưu tiên `.webp`.
- `cover.alt`: mô tả ảnh ngắn gọn, có ý nghĩa.
- `aliases`: dùng khi đổi URL cũ sang URL mới.

## 5. URL & Slug Convention

URL cần ổn định, dễ đọc và phù hợp với nội dung kỹ thuật.

### Blog

```text
/blog/sql-injection-cheatsheet/
/blog/web-cache-poisoning-overview/
```

### Research

```text
/research/cve-2026-xxxx-analysis/
/research/redis-unauthenticated-access-case-study/
```

### Không dùng

```text
/blog/My First Blog Post/
/blog/SQL Injection!!!/
/blog/post-1/
```

## 6. Page Bundle & Media Workflow

Mỗi bài nên dùng Page Bundle để quản lý nội dung và ảnh.

```text
content/blog/post-name/
├── index.md
├── cover.webp
├── image-01.webp
├── poc-01.webp
└── diagram-01.webp
```

Quy ước:

- Ảnh cover đặt tên là `cover.webp`.
- Ảnh trong bài dùng tên rõ nghĩa hoặc đánh số: `image-01.webp`, `poc-01.webp`.
- Ưu tiên `.webp` để giảm dung lượng.
- Ảnh trong body cần có `alt text` hoặc caption khi cần.
- Không upload ảnh thừa, ảnh test hoặc ảnh chưa dùng.

## 7. Pre-Publish Checklist

Trước khi publish, kiểm tra:

```text
[ ] Nội dung đã hoàn chỉnh
[ ] Title rõ ràng
[ ] Slug đúng quy ước
[ ] Summary đã có
[ ] Description đã có
[ ] Date đúng
[ ] Lastmod đúng
[ ] Draft = false
[ ] Tags đầy đủ
[ ] Categories đầy đủ
[ ] Cover image đúng tên
[ ] Cover alt text đã có
[ ] Ảnh trong bài hiển thị đúng
[ ] Không còn placeholder/TODO
[ ] Không còn link lỗi rõ ràng
[ ] TOC bật nếu bài dài
```

## 8. Local Build Checklist

Trước khi push, chạy build local:

```bash
hugo
hugo --minify
```

Cần kiểm tra:

- Không có error.
- Không có warning nghiêm trọng.
- Không lỗi thiếu ảnh.
- Không lỗi shortcode.
- Không lỗi cấu hình front matter.

Nếu build lỗi, sửa trước khi commit.

## 9. Git Workflow

Quy trình Git cơ bản:

```text
Edit content
    ↓
Run local build
    ↓
Commit
    ↓
Push
    ↓
Check GitHub Actions
```

Commit message nên rõ loại thay đổi:

```text
content(blog): publish sql injection cheatsheet
content(research): add redis case study
fix(blog): update cover image
seo: update description metadata
media: optimize blog images
```

Không nên commit với message chung chung:

```text
update
fix
new post
change file
```

## 10. GitHub Actions & Deploy Check

Sau khi push, kiểm tra GitHub Actions:

```text
GitHub Repository
    ↓
Actions
    ↓
Latest workflow run
    ↓
Confirm status = Success
```

Nếu workflow fail:

- Đọc log lỗi.
- Xác định lỗi do Hugo, theme, file path, front matter hay asset.
- Sửa lỗi local.
- Chạy lại `hugo --minify`.
- Commit và push lại.

## 11. Post-Publish Checklist

Sau khi deploy thành công, kiểm tra trên website live:

```text
[ ] Bài viết mở được
[ ] URL đúng
[ ] Title hiển thị đúng
[ ] Cover/thumbnail hiển thị đúng
[ ] Nội dung không vỡ layout
[ ] Ảnh trong bài hiển thị đúng
[ ] Code block đọc được
[ ] Table hiển thị tốt
[ ] Mobile layout ổn
[ ] Blog/Research list có bài mới
[ ] Search tìm được bài
[ ] Tags/Categories hoạt động
[ ] RSS cập nhật
[ ] Sitemap cập nhật
[ ] robots.txt không chặn nhầm
[ ] 404 page vẫn hoạt động
```

## 12. Social Preview Checklist

Khi chia sẻ bài viết, kiểm tra preview trên các nền tảng chính:

- Facebook
- LinkedIn
- X/Twitter
- Discord
- Zalo nếu cần

Preview cần đúng:

```text
[ ] Title
[ ] Description
[ ] Cover image
[ ] Canonical URL
```

Nếu preview sai, kiểm tra lại:

- `title`
- `description`
- `cover.image`
- OpenGraph metadata
- Cache preview của nền tảng social

## 13. Update Existing Post

Khi cập nhật bài cũ:

- Giữ nguyên slug nếu không cần đổi URL.
- Cập nhật `lastmod`.
- Nếu đổi URL, thêm URL cũ vào `aliases`.
- Kiểm tra lại ảnh, link, tag và category.
- Chạy lại build trước khi push.

Ví dụ:

```yaml
lastmod: "2026-07-06"
aliases:
  - /blog/old-post-url/
```

## 14. Rollback Workflow

Nếu bài mới gây lỗi nghiêm trọng:

```text
Option 1: Sửa lỗi và push commit mới
Option 2: Đặt draft = true để ẩn bài
Option 3: Revert commit gần nhất
```

Ưu tiên thứ tự:

1. Sửa lỗi nhanh nếu lỗi nhỏ.
2. Đưa bài về draft nếu nội dung chưa sẵn sàng.
3. Revert commit nếu lỗi ảnh hưởng toàn site.

## 15. Backup & Source of Truth

Nguồn chính của website là GitHub repository.

Cần duy trì:

- Nội dung trong `content/`.
- Ảnh trong Page Bundle.
- Cấu hình Hugo.
- Layout override.
- Tài liệu trong `docs/`.

Không chỉnh sửa trực tiếp file build output nếu có thể tránh. Mọi thay đổi quan trọng nên đi qua source code và Git commit.

## 16. Publishing Definition of Done

Một bài được xem là publish hoàn chỉnh khi:

```text
[ ] Nội dung hoàn chỉnh
[ ] Front matter đầy đủ
[ ] Media đúng chuẩn
[ ] Local build thành công
[ ] Commit message rõ ràng
[ ] GitHub Actions success
[ ] Website live hiển thị đúng
[ ] Mobile layout ổn
[ ] Search/RSS/Sitemap cập nhật
[ ] Social preview đúng
```

## 17. Ghi chú vận hành

- Viết ít nhưng publish đều.
- Không publish bài thiếu metadata.
- Không đổi slug tùy tiện sau khi bài đã index.
- Không upload ảnh dung lượng lớn nếu không cần thiết.
- Mỗi bài nên có một keyword/topic chính.
- Ưu tiên chất lượng, tính nhất quán và khả năng bảo trì lâu dài.
