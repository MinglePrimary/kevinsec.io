# KevinSec Blog — Content Architecture Handbook

> Version: Lesson 9 aligned with Decap CMS `config.yml`  
> Scope: Content architecture, front matter convention, Page Bundle workflow, taxonomy, and writing rules for `kevinsec.io`.

---

## 1. Mục tiêu

Tài liệu này định nghĩa quy chuẩn tổ chức nội dung cho KevinSec Blog.

Mục tiêu chính:

- Giữ nội dung blog, research và page thống nhất với cấu hình Decap CMS.
- Tránh tình trạng mỗi bài có front matter khác nhau.
- Chuẩn hóa cách dùng Page Bundle: mỗi bài là một thư mục riêng.
- Chuẩn hóa category, tag, slug, cover image và internal linking.
- Biến `kevinsec.io` thành một Knowledge Base có cấu trúc cho Offensive Security, Web Security, Research và Career.

---

## 2. Source of Truth

Trong project KevinSec, có 3 nguồn cần đồng bộ:

```text
static/admin/config.yml          # Decap CMS config
archetypes/blog/index.md         # Hugo archetype cho Blog
archetypes/research/index.md     # Hugo archetype cho Research
docs/content-architecture.md     # Handbook này
```

Quy tắc:

```text
Decap CMS config là source of truth cho field khi tạo bài từ CMS.
Archetype là source of truth khi tạo bài bằng Hugo CLI.
Handbook này là source of truth cho cách đặt tên, taxonomy, slug, cover và content structure.
```

Nếu sửa category, tag hoặc front matter convention, cần kiểm tra lại cả 3 nơi trên.

---

## 3. Content Model tổng thể

KevinSec hiện có 3 nhóm nội dung chính trong Decap CMS:

```text
collections:
  - blog
  - research
  - pages
```

Tương ứng với cấu trúc Hugo:

```text
content/
├── _index.md
├── blog/
│   └── <slug>/
│       ├── index.md
│       └── cover.png
├── research/
│   └── <slug>/
│       ├── index.md
│       └── cover.png
├── services/
│   └── _index.md
├── about/
│   └── index.md
└── contact/
    └── index.md
```

---

## 4. Page Bundle Convention

Blog và Research đều dùng Hugo Page Bundle.

Chuẩn bắt buộc:

```text
content/blog/<slug>/index.md
content/research/<slug>/index.md
```

Ví dụ:

```text
content/blog/web-pentest-checklist/index.md
content/blog/web-pentest-checklist/cover.png

content/research/cve-2026-example/index.md
content/research/cve-2026-example/cover.png
```

Quy tắc:

- Mỗi bài viết là một thư mục riêng.
- File Markdown chính luôn là `index.md`.
- Ảnh cover và ảnh minh họa nên đặt cùng thư mục với bài viết.
- Ưu tiên tên ảnh đơn giản: `cover.png`, `diagram.png`, `request.png`, `response.png`.
- Không nên gom tất cả ảnh bài viết vào một thư mục global nếu ảnh chỉ dùng cho một bài.

---

## 5. Blog Collection

### 5.1 Mục đích

Dùng cho các bài viết dạng:

```text
- Web Security note
- Pentest tutorial
- Tool usage
- Recon workflow
- Career note
- KevinSec update
- Writeup ngắn
- Knowledge base article
```

### 5.2 Front Matter chuẩn cho Blog

```yaml
---
title: ""
slug: ""
date: 2026-07-04T00:00:00+07:00
lastmod: 2026-07-04T00:00:00+07:00
draft: true

description: ""
summary: ""

cover:
  image: "cover.png"
  alt: ""
  caption: ""
  relative: true
  hidden: false
  hiddenInList: false
  hiddenInSingle: false

categories:
  - "Cybersecurity"

tags: []

author: "KevinSec"

ShowToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true

canonicalURL: ""
---
```

### 5.3 Category được phép dùng cho Blog

Theo `config.yml`, Blog collection đang dùng các category sau:

```text
Cybersecurity
Web Security
Pentest
Red Team
Bug Bounty
Recon
Security Tools
Career
KevinSec Updates
```

### 5.4 Quy tắc category cho Blog

- Mỗi bài nên có 1 category chính.
- Có thể dùng 2 category nếu bài thật sự giao thoa giữa 2 nhóm.
- Không dùng tag làm category.
- Không tạo category mới trong Decap nếu chưa cập nhật handbook.

Ví dụ đúng:

```yaml
categories:
  - "Web Security"

tags:
  - xss
  - dom-xss
  - javascript
```

Ví dụ không nên:

```yaml
categories:
  - "XSS"
  - "DOM XSS"
  - "JavaScript"
```

---

## 6. Research Collection

### 6.1 Mục đích

Dùng cho các bài nghiên cứu hoặc bài có tính kỹ thuật sâu hơn Blog:

```text
- Vulnerability Research
- CVE Research
- Security Advisory
- Exploit Analysis
- Technical Deep Dive
- Tool Research
- Attack Surface Research
- Case Study
```

### 6.2 Front Matter chuẩn cho Research

```yaml
---
title: ""
slug: ""
date: 2026-07-04T00:00:00+07:00
lastmod: 2026-07-04T00:00:00+07:00
draft: true

research_type: "Vulnerability Research"
status: "In Progress"
severity: ""
cve: ""
cvss:
vendor: ""
product: ""
affected_versions: ""
disclosure_date:

description: ""
summary: ""

cover:
  image: "cover.png"
  alt: ""
  caption: ""
  relative: true
  hidden: false
  hiddenInList: false
  hiddenInSingle: false

categories:
  - "Research"

tags: []

references: []

ShowToc: true
TocOpen: true
ShowReadingTime: true
ShowBreadCrumbs: true

canonicalURL: ""
---
```

### 6.3 Research Type được phép dùng

Theo `config.yml`:

```text
Vulnerability Research
CVE Research
Security Advisory
Exploit Analysis
Technical Deep Dive
Tool Research
Attack Surface Research
Case Study
```

### 6.4 Research Status được phép dùng

Theo `config.yml`:

```text
Idea
In Progress
Under Review
Vendor Notified
Embargoed
Published
Archived
```

### 6.5 Severity được phép dùng

Theo `config.yml`:

```text
Informational
Low
Medium
High
Critical
```

### 6.6 Category được phép dùng cho Research

Theo `config.yml`:

```text
Research
Web Security
Network Security
API Security
Cloud Security
Mobile Security
Red Team
Vulnerability Disclosure
```

### 6.7 Quy tắc Research

- Chỉ dùng Research collection cho nội dung có cấu trúc kỹ thuật rõ.
- Nếu là ghi chú học tập ngắn, dùng Blog.
- Nếu có CVE, điền `cve`.
- Nếu chưa có CVE, để trống `cve`.
- Nếu có CVSS, dùng số từ `0` đến `10`.
- Nếu chưa disclosure công khai, giữ `draft: true` hoặc `status: "Embargoed"`.
- Không public PoC nhạy cảm khi vendor chưa fix hoặc chưa hết embargo.

---

## 7. Website Pages

Decap CMS hiện quản lý các page sau:

```text
content/_index.md              # Home Page
content/services/_index.md     # Services Page
content/about/index.md         # About Page
content/contact/index.md       # Contact Page
```

### 7.1 Home Page

Dùng cho landing page chính của KevinSec.

Các field chính:

```yaml
title: "KevinSec"
description: ""
profileMode:
  enabled: true
  title: "KevinSec"
  subtitle: ""
  imageUrl: ""
  imageWidth: 160
  imageHeight: 160
  buttons: []
```

Nội dung nên thể hiện:

```text
- KevinSec là ai
- Tập trung vào lĩnh vực gì
- Blog / Research / Services
- Call-to-action chính
```

### 7.2 Services Page

File:

```text
content/services/_index.md
```

Dùng cho dịch vụ:

```text
- Web Application Pentest
- API Security Assessment
- Attack Surface Review
- Red Team / Adversary Simulation
- Security Training
- Vulnerability Research
```

### 7.3 About Page

File:

```text
content/about/index.md
```

Dùng để giới thiệu:

```text
- KevinSec là gì
- Background offensive security
- Research interests
- Values
- Contact direction
```

### 7.4 Contact Page

File:

```text
content/contact/index.md
```

Dùng cho:

```text
- Email
- GitHub
- LinkedIn
- Inquiry instruction
```

---

## 8. Taxonomy Strategy

### 8.1 Category

Category là nhóm nội dung cấp cao.

Blog categories:

```text
Cybersecurity
Web Security
Pentest
Red Team
Bug Bounty
Recon
Security Tools
Career
KevinSec Updates
```

Research categories:

```text
Research
Web Security
Network Security
API Security
Cloud Security
Mobile Security
Red Team
Vulnerability Disclosure
```

Quy tắc:

```text
Category = nhóm lớn.
Tag = chủ đề chi tiết.
Research Type = loại bài nghiên cứu.
Status = trạng thái nghiên cứu.
Severity = mức độ nghiêm trọng.
```

### 8.2 Tags

Tag nên viết:

```text
lowercase
dùng dấu gạch ngang
không dấu tiếng Việt
không trùng biến thể
```

Ví dụ đúng:

```text
xss
dom-xss
sql-injection
idor
api-security
jwt
oauth
ssrf
path-traversal
burp-suite
nuclei
recon
attack-surface
```

Ví dụ không nên:

```text
SQLi
sql injection
SQL-Injection
kiểm-thử-web
```

Chọn một chuẩn duy nhất. Khuyến nghị dùng:

```text
sql-injection
```

Không dùng đồng thời:

```text
sqli
sql-injection
sql injection
```

---

## 9. Tag Library đề xuất

### 9.1 Web Security

```text
web-security
xss
reflected-xss
stored-xss
dom-xss
sql-injection
blind-sql-injection
csrf
ssrf
idor
broken-access-control
file-upload
path-traversal
lfi
rfi
ssti
xxe
open-redirect
host-header-injection
request-smuggling
cors
clickjacking
jwt
oauth
graphql
api-security
```

### 9.2 Recon

```text
recon
subdomain-enumeration
asset-discovery
attack-surface
fingerprinting
nuclei
ffuf
amass
httpx
katana
wayback
```

### 9.3 Red Team

```text
red-team
initial-access
privilege-escalation
post-exploitation
active-directory
lateral-movement
persistence
opsec
phishing
c2
```

### 9.4 Tools

```text
security-tools
burp-suite
sqlmap
nuclei
ffuf
dirsearch
feroxbuster
amass
httpx
katana
dalfox
xsstrike
metasploit
trufflehog
```

### 9.5 Career

```text
career
interview
portfolio
freelance
bug-bounty-career
oscp
cpts
learning-plan
personal-branding
```

### 9.6 Hugo / Blog Platform

```text
hugo
decap-cms
papermod
github-pages
seo
content-workflow
```

---

## 10. URL Slug Convention

Slug phải ngắn, rõ, dùng tiếng Anh, lowercase, dùng dấu gạch ngang.

Ví dụ tốt:

```text
web-pentest-checklist
dom-xss-basics
jwt-attack-patterns
nuclei-template-workflow
attack-surface-management-notes
```

Không nên:

```text
post-1
new-post
hello-world
bai-viet-moi
huong-dan-kiem-thu-xss-co-ban-rat-dai
```

Quy tắc:

```text
- Dùng 3–6 từ.
- Không dùng ký tự đặc biệt.
- Không dùng ngày tháng nếu không cần.
- Không đổi slug sau khi publish nếu bài đã index.
```

---

## 11. Cover Image Convention

Theo Decap config, Blog và Research đều có object:

```yaml
cover:
  image: ""
  alt: ""
  caption: ""
  relative: true
  hidden: false
  hiddenInList: false
  hiddenInSingle: false
```

### 11.1 Ý nghĩa

| Field | Ý nghĩa |
|---|---|
| `image` | Đường dẫn ảnh cover |
| `alt` | Mô tả ảnh cho accessibility và SEO |
| `caption` | Chú thích ảnh |
| `relative` | `true` nếu ảnh nằm cùng Page Bundle |
| `hidden` | Ẩn cover hoàn toàn |
| `hiddenInList` | Ẩn cover ở trang danh sách |
| `hiddenInSingle` | Ẩn cover trong trang bài viết |

### 11.2 Quy tắc dùng cover

Khuyến nghị mặc định:

```yaml
cover:
  image: "cover.png"
  alt: "Short description of the cover image"
  caption: ""
  relative: true
  hidden: false
  hiddenInList: false
  hiddenInSingle: false
```

Khi cover làm thumbnail quá lớn ở list:

```yaml
hiddenInList: true
```

Khi muốn giữ thumbnail ngoài list nhưng không hiện ảnh lớn trong bài:

```yaml
hiddenInSingle: true
```

Khi không muốn dùng cover:

```yaml
hidden: true
```

---

## 12. Writing Structure cho Blog

Template khuyến nghị:

```markdown
# Overview

Tóm tắt bài viết giải quyết vấn đề gì.

# Context

Bối cảnh kỹ thuật hoặc vấn đề thực tế.

# Main Content

Nội dung chính.

# Key Takeaways

- Ý chính 1
- Ý chính 2
- Ý chính 3

# References

# Related Articles
```

Với bài lỗ hổng web, có thể dùng cấu trúc KevinSec ưu tiên:

```markdown
# Context

# Root Cause

# Impact

# Signature

# How to Exploit

# Detection

# Mitigation

# References

# Related Articles
```

---

## 13. Writing Structure cho Research

Template khuyến nghị:

```markdown
# Summary

# Context

# Root Cause

# Impact

# Technical Details

# Proof of Concept

# Detection

# Mitigation

# References

# Related Articles
```

Quy tắc:

- Research phải có bằng chứng kỹ thuật rõ.
- Không viết Research như blog cảm nghĩ.
- Nếu liên quan disclosure, phải kiểm tra trạng thái public trước khi publish.
- Nếu có PoC nhạy cảm, cân nhắc rút gọn hoặc mô tả ở mức an toàn.

---

## 14. Internal Linking

Mỗi bài nên có phần cuối:

```markdown
# Related Articles

- [Related article 1](/blog/example-1/)
- [Related article 2](/research/example-2/)
```

Quy tắc:

- Mỗi bài nên link 2–5 bài liên quan.
- Link từ bài mới về bài cũ.
- Khi update bài cũ, thêm link ngược về bài mới nếu phù hợp.
- Ưu tiên link theo cùng topic, không link ngẫu nhiên.

Ví dụ:

```text
Bài DOM XSS nên link tới:
- XSS Basics
- Reflected XSS
- CSP Bypass
- JavaScript Source/Sink
```

---

## 15. Pillar → Cluster Model

KevinSec nên tổ chức nội dung theo Pillar và Cluster.

Ví dụ:

```text
Web Security
├── XSS
│   ├── XSS Basics
│   ├── Reflected XSS
│   ├── Stored XSS
│   ├── DOM XSS
│   └── CSP Bypass
├── SQL Injection
│   ├── SQL Injection Basics
│   ├── Error-based SQLi
│   ├── Blind SQLi
│   └── SQLMap Workflow
└── Access Control
    ├── IDOR
    ├── Broken Access Control
    └── Authorization Testing
```

Pillar đề xuất:

```text
Web Security
API Security
Recon
Bug Bounty
Red Team
Security Tools
Vulnerability Research
Career
KevinSec Build Log
```

---

## 16. Decap CMS Workflow

Khi tạo bài bằng Decap CMS:

```text
1. Chọn Blog Posts hoặc Research Articles.
2. Nhập Title.
3. Nhập Slug bằng tiếng Anh, lowercase, dấu gạch ngang.
4. Giữ Draft = true khi chưa publish.
5. Viết Description và Summary.
6. Upload Cover nếu cần.
7. Chọn Categories từ danh sách có sẵn.
8. Nhập Tags theo chuẩn lowercase.
9. Bật/tắt TOC, Reading Time, Breadcrumbs nếu cần.
10. Viết Content.
11. Preview local hoặc publish.
12. Chuyển Draft = false khi sẵn sàng.
```

---

## 17. Hugo CLI Workflow

Khi tạo bài bằng Hugo CLI:

Blog:

```bash
hugo new content blog/my-blog-post/index.md
```

Research:

```bash
hugo new content research/my-research-note/index.md
```

Sau đó kiểm tra:

```text
- Front matter có khớp Decap config không?
- Path có đúng Page Bundle không?
- Cover image có nằm cùng thư mục không?
- Category có nằm trong danh sách được phép không?
- Tag có đúng quy chuẩn không?
```

---

## 18. Field Naming Convention

KevinSec hiện ưu tiên field theo PaperMod:

```yaml
ShowToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true
```

Không dùng lẫn lộn:

```yaml
showToc: true
showReadingTime: true
showBreadCrumbs: true
```

Quy tắc:

```text
Dùng đúng field đang có trong Decap config.
Không tự đổi case của field nếu chưa cập nhật config.yml và archetype.
```

---

## 19. Publish Checklist

Trước khi publish, kiểm tra:

```text
[ ] title rõ ràng
[ ] slug ngắn, tiếng Anh, lowercase
[ ] draft đã chuyển false khi publish
[ ] description có nội dung
[ ] summary có nội dung
[ ] category đúng danh sách Decap
[ ] tags đúng lowercase/kebab-case
[ ] cover có alt text nếu có ảnh
[ ] ShowToc phù hợp
[ ] bài có References nếu có thông tin kỹ thuật
[ ] bài có Related Articles nếu có bài liên quan
[ ] không public thông tin nhạy cảm hoặc PoC chưa được phép
```

---

## 20. Definition of Done

Lesson 9 được xem là hoàn tất khi:

```text
[ ] Decap config tạo Blog theo Page Bundle
[ ] Decap config tạo Research theo Page Bundle
[ ] Blog front matter khớp handbook
[ ] Research front matter khớp handbook
[ ] Có archetypes/blog/index.md
[ ] Có archetypes/research/index.md
[ ] Category trong handbook khớp config.yml
[ ] Cover field trong handbook khớp config.yml
[ ] PaperMod field dùng thống nhất: ShowToc, TocOpen, ShowReadingTime, ShowBreadCrumbs
[ ] Có quy tắc slug, tag, cover, internal link
```

---

## 21. Ghi chú vận hành

- Decap CMS không dùng Hugo archetype khi tạo bài.
- Hugo archetype chỉ dùng khi tạo bài bằng command line.
- Vì vậy Decap config và archetype phải được giữ đồng bộ thủ công.
- Nếu sau này thêm collection mới như `writeups`, `notes`, hoặc `tools`, cần cập nhật:
  - `static/admin/config.yml`
  - `archetypes/<section>/index.md`
  - Handbook này
  - Menu/taxonomy nếu cần

---

## 22. Roadmap liên quan

Tài liệu này thuộc:

```text
Lesson 9 — Front Matter + Archetype
Lesson 9.5 — Content Architecture Handbook
```

Sau khi hoàn tất, chuyển sang:

```text
Lesson 10 — Xây Home Page KevinSec
```
