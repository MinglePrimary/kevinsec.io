# KevinSec Media Workflow

Tài liệu này chuẩn hóa cách quản lý ảnh và media cho blog KevinSec sử dụng Hugo, PaperMod và Decap CMS.

Mục tiêu:

- Mỗi bài viết quản lý ảnh theo Page Bundle.
- Ảnh cover hiển thị đúng trên bài viết, Blog list, Research list và Home Page.
- Tên file rõ ràng, dễ bảo trì, thân thiện SEO.
- Ảnh trong nội dung có `alt`, caption, lazy loading và responsive.
- Workflow đủ đơn giản để viết bài qua Decap CMS mà không cần sửa HTML thủ công.

---

## 1. Nguyên tắc tổng quát

KevinSec sử dụng mô hình **Leaf Bundle** của Hugo.

Mỗi bài viết là một thư mục riêng, file Markdown chính luôn là `index.md`, ảnh của bài viết nằm cùng thư mục với bài viết đó.

Ví dụ:

```text
content/
├── blog/
│   └── sample-blog-post/
│       ├── index.md
│       ├── cover.png
│       ├── request.png
│       └── response.png
│
└── research/
    └── cve-2026-example/
        ├── index.md
        ├── cover.png
        ├── diagram.png
        └── poc-request.png
```

Quy tắc:

- Mỗi bài viết là một thư mục riêng.
- File Markdown chính luôn là `index.md`.
- Ảnh cover và ảnh minh họa đặt cùng thư mục với bài viết.
- Không gom ảnh dùng riêng cho một bài vào thư mục global như `/static/images/`.
- Không dùng đường dẫn kiểu `../../image.png` trong bài viết.
- Không trỏ ảnh bằng path đầy đủ như `/content/blog/post-name/image.png`.

---

## 2. Cấu trúc chuẩn cho Blog

Mỗi bài blog nên có cấu trúc:

```text
content/blog/post-slug/
├── index.md
├── cover.png
├── diagram.png
├── request.png
└── response.png
```

Ví dụ:

```text
content/blog/testing-cover-image/
├── index.md
├── cover.png
└── burp-request.png
```

Front matter mẫu:

```yaml
---
title: "Testing Cover Image"
date: 2026-07-06
draft: false
summary: "A short note for testing cover image and page bundle workflow."
tags: ["hugo", "media", "workflow"]
categories: ["Blog"]

cover:
  image: cover.png
  alt: "Testing Cover Image cover"
  caption: ""
  relative: true
---
```

---

## 3. Cấu trúc chuẩn cho Research

Mỗi research note nên có cấu trúc:

```text
content/research/research-slug/
├── index.md
├── cover.png
├── architecture.png
├── poc-request.png
└── poc-response.png
```

Ví dụ:

```text
content/research/cve-2026-example/
├── index.md
├── cover.png
├── diagram.png
└── exploit-flow.png
```

Front matter mẫu:

```yaml
---
title: "CVE-2026 Example Research Note"
date: 2026-07-06
draft: false
summary: "A sample research note for testing media workflow in Hugo Page Bundle."
tags: ["cve", "research", "web-security"]
categories: ["Research"]

cover:
  image: cover.png
  alt: "CVE-2026 Example research cover"
  caption: ""
  relative: true
---
```

---

## 4. Quy ước đặt tên ảnh

Tên file ảnh phải:

- Dùng chữ thường.
- Dùng `kebab-case`.
- Không dùng khoảng trắng.
- Không dùng tiếng Việt có dấu.
- Không dùng tên mặc định từ screenshot tool.
- Phản ánh đúng nội dung ảnh.

Nên dùng:

```text
cover.png
diagram.png
architecture.png
workflow.png
request.png
response.png
burp-request.png
burp-response.png
login-page.png
exploit-flow.png
attack-chain.png
poc-request.png
poc-response.png
```

Không nên dùng:

```text
Screenshot 2026-06-29 101758.png
screenshot-2026-06-29-101758.png
image1.png
abc.png
test.png
attacker_red.png
IMG_1234.PNG
```

Nếu một bài có nhiều ảnh request/response, đánh số rõ ràng:

```text
request-01.png
response-01.png
request-02.png
response-02.png
```

---

## 5. Quy ước cover image

Mỗi bài Blog và Research nên có một file cover.

Tên chuẩn:

```text
cover.png
```

Hoặc nếu đã tối ưu sang WebP:

```text
cover.webp
```

Front matter tương ứng:

```yaml
cover:
  image: cover.png
  alt: "Mô tả ngắn gọn ảnh cover"
  caption: ""
  relative: true
```

Khuyến nghị:

- Tỷ lệ ảnh: `16:9`.
- Chiều rộng hợp lý: khoảng `1200px`.
- Dung lượng nên giữ dưới `300KB` nếu có thể.
- `alt` phải mô tả nội dung ảnh, không nhồi keyword.
- Không dùng cover có chữ quá nhỏ vì sẽ khó đọc trên mobile.

---

## 6. Cách chèn ảnh trong Markdown

Có 2 cách chèn ảnh.

### Cách 1: Markdown cơ bản

Dùng khi ảnh đơn giản, không cần caption:

```markdown
![Burp Suite request](burp-request.png)
```

Không dùng:

```markdown
![](burp-request.png)
```

Lý do: ảnh phải có `alt text` để hỗ trợ SEO và accessibility.

---

### Cách 2: Shortcode `img`

Dùng khi cần caption, căn giữa, lazy loading và responsive.

Cú pháp chuẩn:

```go-html-template
{{< img src="burp-request.png" alt="Burp Suite HTTP request" caption="Figure 1. HTTP request captured in Burp Suite" >}}
```

Ví dụ ảnh sơ đồ:

```go-html-template
{{< img src="attack-flow.png" alt="Attack flow diagram" caption="Figure 2. Attack flow from initial request to data exposure" >}}
```

Ví dụ ảnh nhỏ:

```go-html-template
{{< img src="tool-icon.png" alt="Tool icon" caption="Tool icon" class="small" >}}
```

Ví dụ ảnh rộng:

```go-html-template
{{< img src="architecture.png" alt="System architecture diagram" caption="Figure 3. System architecture" class="wide" >}}
```

---

## 7. Shortcode `img.html`

File shortcode đặt tại:

```text
layouts/shortcodes/img.html
```

Shortcode này có nhiệm vụ:

- Tìm ảnh trong Page Bundle bằng `.Page.Resources.GetMatch`.
- Tự tạo ảnh responsive nếu ảnh xử lý được.
- Thêm `loading="lazy"`.
- Thêm `decoding="async"`.
- Render `alt`, `title`, `caption`.
- Hỗ trợ class tùy chọn như `small`, `medium`, `large`, `wide`.

Cách gọi:

```go-html-template
{{< img src="request.png" alt="HTTP request" caption="Figure 1. HTTP request" >}}
```

Quy tắc quan trọng:

- `src` chỉ ghi tên file trong cùng thư mục Page Bundle.
- Không ghi `/content/blog/...`.
- Không ghi `../../image.png`.
- Không để trống `alt` trừ khi ảnh chỉ mang tính trang trí.

---

## 8. Lazy loading là gì?

Lazy loading giúp trình duyệt chỉ tải ảnh khi người dùng sắp cuộn đến ảnh đó.

Ví dụ bài viết có 20 ảnh, người dùng mới mở phần đầu bài thì trình duyệt chưa cần tải toàn bộ 20 ảnh ngay lập tức.

HTML tương ứng:

```html
<img src="request.png" loading="lazy">
```

Lợi ích:

- Tải trang nhanh hơn.
- Tiết kiệm băng thông.
- Cải thiện trải nghiệm mobile.
- Hữu ích với các bài Research có nhiều screenshot.

Lưu ý:

- Ảnh trong nội dung bài nên dùng lazy loading.
- Ảnh hero/cover ở đầu trang có thể không cần lazy loading nếu muốn ưu tiên tốc độ hiển thị ban đầu.

---

## 9. Responsive image là gì?

Responsive image là kỹ thuật tạo nhiều kích thước cho cùng một ảnh để trình duyệt tự chọn phiên bản phù hợp.

Ví dụ:

```text
request-480.png
request-800.png
request-1200.png
```

Mobile có thể tải ảnh nhỏ, desktop có thể tải ảnh lớn hơn.

HTML thường có dạng:

```html
<img
  src="request-800.png"
  srcset="request-480.png 480w, request-800.png 800w, request-1200.png 1200w"
  sizes="(max-width: 768px) 100vw, 800px"
  alt="HTTP request">
```

Lợi ích:

- Giảm dung lượng tải trên mobile.
- Giữ ảnh sắc nét trên desktop.
- Tốt hơn cho Core Web Vitals.

Trong KevinSec, responsive image nên được xử lý qua Hugo shortcode, không cần viết HTML thủ công trong bài.

---

## 10. Thumbnail và Home Page

Cover của bài viết được dùng cho:

- Trang chi tiết bài viết.
- Blog list: `/blog/`.
- Research list: `/research/`.
- Featured Blog trên Home Page.
- Featured Research trên Home Page.

Do đó, mỗi bài nên có:

```yaml
cover:
  image: cover.png
  alt: "Mô tả ảnh cover"
  caption: ""
  relative: true
```

Và file thật phải tồn tại:

```text
content/blog/post-slug/cover.png
```

hoặc:

```text
content/research/research-slug/cover.png
```

Nếu thumbnail không hiện, kiểm tra các lỗi thường gặp:

- Sai tên file: `cover.png` nhưng file thật là `Cover.png`.
- Sai extension: front matter ghi `.png` nhưng file thật là `.webp`.
- File cover không nằm cùng thư mục với `index.md`.
- Front matter thiếu block `cover`.
- Template Home Page chưa dùng `.Resources.GetMatch` để lấy ảnh từ Page Bundle.

---

## 11. CSS cho ảnh trong nội dung

File CSS nên đặt tại:

```text
assets/css/extended/kevinsec-image.css
```

Mẫu CSS:

```css
.kevinsec-img {
  margin: 2rem auto;
  text-align: center;
}

.kevinsec-img img {
  max-width: 100%;
  height: auto;
  border-radius: 8px;
  display: block;
  margin: 0 auto;
}

.kevinsec-img figcaption {
  margin-top: 0.6rem;
  font-size: 0.9rem;
  line-height: 1.5;
  opacity: 0.75;
  text-align: center;
}

.kevinsec-img.wide img {
  width: 100%;
}

.kevinsec-img.small img {
  max-width: 480px;
}

.kevinsec-img.medium img {
  max-width: 720px;
}

.kevinsec-img.large img {
  max-width: 960px;
}
```

---

## 12. CSS cho thumbnail card

File CSS có thể đặt tại:

```text
assets/css/extended/kevinsec-home-card.css
```

Mẫu CSS:

```css
.home-card-cover {
  display: block;
  overflow: hidden;
  border-radius: 12px;
  margin-bottom: 1rem;
  background: var(--entry);
}

.home-card-cover img {
  display: block;
  width: 100%;
  aspect-ratio: 16 / 9;
  object-fit: cover;
  height: auto;
}

.home-card-meta {
  margin: 0.25rem 0 0.75rem;
  font-size: 0.85rem;
  opacity: 0.7;
}
```

Mục tiêu:

- Thumbnail có cùng tỷ lệ `16:9`.
- Layout card không bị lệch chiều cao quá nhiều.
- Mobile không bị ảnh quá to hoặc vỡ layout.

---

## 13. Decap CMS media workflow

Khi tạo bài qua Decap CMS, ảnh nên được upload vào đúng thư mục bài viết.

Mô hình mong muốn:

```text
content/blog/my-new-post/
├── index.md
├── cover.png
└── diagram.png
```

Không mong muốn:

```text
static/images/cover.png
static/uploads/diagram.png
```

Với Blog và Research, Decap collection nên tạo dạng folder-based Page Bundle.

Nguyên tắc:

- Tạo bài mới → tạo thư mục riêng.
- Upload cover → lưu vào cùng thư mục bài.
- Upload ảnh minh họa → lưu vào cùng thư mục bài.
- Trường `cover.image` chỉ lưu tên file, ví dụ `cover.png`.

---

## 14. Checklist trước khi publish bài viết

Trước khi publish một bài Blog hoặc Research, kiểm tra:

```text
[ ] Bài viết nằm trong thư mục riêng.
[ ] File chính là index.md.
[ ] Có cover image nếu bài cần hiển thị trên list/home.
[ ] cover.image trỏ đúng tới cover.png hoặc cover.webp.
[ ] File cover nằm cùng thư mục với index.md.
[ ] Tên ảnh dùng lowercase và kebab-case.
[ ] Không còn tên ảnh dạng Screenshot hoặc IMG_1234.
[ ] Ảnh trong bài có alt text.
[ ] Ảnh quan trọng có caption.
[ ] Ảnh trong body dùng shortcode img nếu cần caption/responsive.
[ ] Blog list hiển thị thumbnail đúng.
[ ] Research list hiển thị thumbnail đúng.
[ ] Home Page hiển thị Featured Blog/Research đúng.
[ ] Mobile không bị vỡ layout.
[ ] Chạy hugo --minify không lỗi.
```

---

## 15. Lệnh kiểm thử local

Chạy local server:

```powershell
hugo server -D
```

Mở các trang:

```text
http://localhost:1313/
http://localhost:1313/blog/
http://localhost:1313/research/
```

Build production:

```powershell
hugo --minify
```

Nếu build không lỗi và ảnh hiển thị đúng trên Home, Blog, Research, workflow xem như đạt.

---

## 16. Quy trình viết bài chuẩn

Quy trình đề xuất:

1. Tạo thư mục bài viết.
2. Tạo `index.md`.
3. Thêm `cover.png`.
4. Thêm các ảnh minh họa nếu cần.
5. Khai báo `cover.image` trong front matter.
6. Chèn ảnh trong body bằng Markdown hoặc shortcode `img`.
7. Kiểm tra local bằng `hugo server -D`.
8. Kiểm tra Home, Blog list, Research list.
9. Build bằng `hugo --minify`.
10. Commit và publish.

Ví dụ hoàn chỉnh:

```text
content/research/cve-2026-example/
├── index.md
├── cover.png
├── exploit-flow.png
├── request-01.png
└── response-01.png
```

Trong `index.md`:

```yaml
---
title: "CVE-2026 Example"
date: 2026-07-06
draft: false
summary: "Technical analysis of a sample vulnerability."
tags: ["cve", "web-security"]
categories: ["Research"]

cover:
  image: cover.png
  alt: "CVE-2026 Example cover image"
  caption: ""
  relative: true
---
```

Trong body:

```go-html-template
{{< img src="exploit-flow.png" alt="Exploit flow diagram" caption="Figure 1. Exploit flow" >}}

{{< img src="request-01.png" alt="Proof-of-concept HTTP request" caption="Figure 2. Proof-of-concept HTTP request" >}}
```

---

## 17. Kết luận

Media workflow chuẩn của KevinSec:

```text
content/blog/post-slug/
├── index.md
├── cover.png
├── request.png
└── response.png
```

Front matter:

```yaml
cover:
  image: cover.png
  alt: "Descriptive cover alt text"
  caption: ""
  relative: true
```

Body image:

```go-html-template
{{< img src="request.png" alt="HTTP request" caption="Figure 1. HTTP request" >}}
```

Đây là chuẩn chính thức cho Blog và Research trong KevinSec ở giai đoạn hiện tại.
