# KevinSec Blog — Content Architecture Handbook

## 1. Mục tiêu

Tài liệu này định nghĩa quy chuẩn tổ chức nội dung cho blog `kevinsec.io`.

Mục tiêu chính:

- Giúp blog không bị rời rạc khi số lượng bài viết tăng lên.
- Biến blog thành một Knowledge Base có cấu trúc.
- Giúp người đọc dễ tìm bài liên quan sau khi đọc một bài bất kỳ.
- Giúp tác giả viết bài nhanh hơn nhờ có template và quy tắc cố định.
- Hỗ trợ SEO thông qua category, tag, URL slug và internal linking.

---

## 2. Mô hình tổ chức nội dung

Blog KevinSec nên đi theo mô hình:

```text
Pillar Topic
└── Cluster Articles
    ├── Concept
    ├── Exploitation
    ├── Detection
    ├── Mitigation
    ├── Tools
    └── Case Study / Writeup
```

Ví dụ:

```text
Web Security
└── SQL Injection
    ├── SQL Injection Basics
    ├── Error-based SQL Injection
    ├── Union-based SQL Injection
    ├── Blind SQL Injection
    ├── SQLMap Usage
    └── SQL Injection Mitigation
```

Không nên viết bài rời rạc. Mỗi bài nên thuộc về ít nhất một cụm nội dung lớn.

---

## 3. Categories chuẩn

Category dùng để phân loại cấp cao. Không nên tạo quá nhiều category.

Danh sách category đề xuất:

```text
Blog
Web Security
Red Team
Recon
Tools
Writeups
Research
Career
```

### Ý nghĩa từng category

| Category | Mục đích sử dụng |
|---|---|
| Blog | Bài viết chung, cập nhật cá nhân, định hướng phát triển |
| Web Security | Kiến thức web pentest, OWASP, lỗ hổng web |
| Red Team | Kỹ thuật offensive security, adversary simulation, post-exploitation |
| Recon | Asset discovery, attack surface, subdomain, fingerprinting |
| Tools | Hướng dẫn sử dụng tool, script, automation |
| Writeups | Lab, CTF, bug bounty writeup, case study |
| Research | CVE, phân tích kỹ thuật, vulnerability research |
| Career | Phỏng vấn, định hướng nghề nghiệp, handbook cá nhân |

### Quy tắc category

- Mỗi bài nên có 1 category chính.
- Chỉ thêm category thứ 2 nếu thật sự cần.
- Không tạo category mới nếu có thể dùng category hiện có.
- Category nên viết dạng Title Case, ví dụ: `Web Security`, `Red Team`.

Ví dụ Front Matter:

```yaml
categories:
  - Web Security
```

---

## 4. Tags chuẩn

Tag dùng để mô tả chi tiết nội dung bài viết.

Tag nên viết thường, ngắn gọn, dùng dấu gạch ngang nếu có nhiều từ.

Ví dụ:

```yaml
tags:
  - xss
  - dom-xss
  - javascript
  - burp-suite
```

### Nhóm tag đề xuất

#### Web Security

```text
xss
reflected-xss
stored-xss
dom-xss
sqli
blind-sqli
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

#### Recon

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

#### Red Team

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

#### Tools

```text
tools
burp-suite
sqlmap
nuclei
ffuf
dirsearch
feroxbuster
amass
httpx
katana
trufflehog
python
bash
automation
```

#### Research / CVE

```text
vulnerability-research
cve
root-cause-analysis
patch-diffing
source-code-review
exploit-development
responsible-disclosure
```

#### Career

```text
career
interview
pentest
red-team-career
oscp
cpts
freelance
personal-branding
```

### Quy tắc tag

- Mỗi bài nên có từ 3 đến 7 tags.
- Không lạm dụng tag quá chi tiết nếu chỉ dùng một lần.
- Dùng thống nhất một format. Ví dụ: dùng `sqli`, không dùng xen kẽ `sql-injection`, `SQLi`, `SQL Injection`.
- Tag phải giúp người đọc tìm thêm bài liên quan.

---

## 5. Quy chuẩn URL slug

URL slug nên ngắn, rõ nghĩa và chứa keyword chính.

### Format đề xuất

```text
/blog/<main-keyword>/
/blog/<topic>-<specific-angle>/
/blog/<tool>-guide/
```

### Ví dụ tốt

```text
/blog/sql-injection-basics/
/blog/dom-xss-guide/
/blog/idor-testing-checklist/
/blog/nuclei-recon-workflow/
/blog/burp-suite-for-beginners/
```

### Ví dụ không nên dùng

```text
/blog/post-1/
/blog/test/
/blog/my-first-post/
/blog/new-blog-update/
```

### Quy tắc slug

- Viết thường toàn bộ.
- Dùng dấu `-` để nối từ.
- Không dùng ký tự đặc biệt.
- Không dùng ngày tháng nếu bài không phụ thuộc thời gian.
- Slug nên mô tả trực tiếp nội dung bài viết.

---

## 6. Template bài viết chuẩn

Mỗi bài viết nên đi theo một template cố định để dễ đọc và dễ viết.

### Template cho bài phân tích lỗ hổng

```markdown
# Title

## Introduction

Tóm tắt ngắn gọn lỗ hổng, bối cảnh và lý do bài viết quan trọng.

## Context

Mô tả bối cảnh kỹ thuật: ứng dụng, request, data flow, trust boundary hoặc thành phần liên quan.

## Root Cause

Giải thích nguyên nhân gốc rễ. Ưu tiên làm rõ điểm xử lý sai với untrusted data.

## Impact

Mô tả tác động bảo mật: đọc dữ liệu, sửa dữ liệu, chiếm quyền, RCE, bypass, data leak, v.v.

## Signature

Dấu hiệu nhận biết lỗ hổng khi pentest:

- Parameter đáng chú ý
- Header đáng chú ý
- Response pattern
- Error message
- Behavior bất thường

## How to Exploit

Mô tả cách khai thác trong môi trường hợp pháp/lab.

## How to Detect

Cách phát hiện bằng manual testing hoặc tool.

## Mitigation

Cách phòng chống hoặc khắc phục.

## References

Nguồn tham khảo chính thống hoặc tài liệu liên quan.

## Related Articles

- [Related Article 1](#)
- [Related Article 2](#)
```

### Template cho bài tool / workflow

```markdown
# Title

## Overview

Tool/workflow này dùng để làm gì.

## Use Case

Khi nào nên dùng.

## Installation

Cách cài đặt cơ bản.

## Basic Usage

Các lệnh cơ bản.

## Practical Workflow

Quy trình sử dụng thực tế.

## Output Analysis

Cách đọc kết quả.

## Common Mistakes

Các lỗi thường gặp.

## References

Nguồn tham khảo.

## Related Articles

- [Related Article 1](#)
```

### Template cho writeup

```markdown
# Title

## Target / Lab Context

Mô tả lab hoặc target hợp pháp.

## Recon

Các bước thu thập thông tin.

## Vulnerability Discovery

Cách phát hiện điểm yếu.

## Exploitation

Cách khai thác trong phạm vi cho phép.

## Post-Exploitation / Impact

Tác động sau khai thác, nếu có.

## Lessons Learned

Điều rút ra sau bài lab/case study.

## Related Articles

- [Related Article 1](#)
```

---

## 7. Internal Linking

Mỗi bài nên có liên kết nội bộ tới bài liên quan.

Mục tiêu:

- Giữ người đọc ở lại blog lâu hơn.
- Giúp Google hiểu cấu trúc nội dung.
- Kết nối các bài viết thành Knowledge Base.

### Vị trí nên đặt internal links

- Trong phần Introduction nếu bài có liên quan trực tiếp.
- Trong phần Context nếu cần giải thích khái niệm nền.
- Cuối bài trong mục `Related Articles`.

### Format đề xuất cuối bài

```markdown
## Related Articles

- [SQL Injection Basics](/blog/sql-injection-basics/)
- [SQLMap Usage Guide](/blog/sqlmap-usage-guide/)
- [Web Pentest Checklist](/blog/web-pentest-checklist/)
```

### Quy tắc internal linking

- Mỗi bài nên có ít nhất 2–3 internal links.
- Không link bừa. Link phải giúp người đọc hiểu sâu hơn.
- Bài pillar nên link đến các bài cluster.
- Bài cluster nên link ngược về bài pillar.

---

## 8. Series bài viết

Series dùng cho các chủ đề cần nhiều bài liên tiếp.

Ví dụ:

```text
Web Pentest 101
├── Part 1: HTTP Request Basics
├── Part 2: Authentication Testing
├── Part 3: Access Control Testing
├── Part 4: XSS Testing
└── Part 5: SQL Injection Testing
```

### Quy tắc series

- Chỉ tạo series khi có ít nhất 3 bài liên quan.
- Tên series nên ổn định, không đổi thường xuyên.
- Mỗi bài trong series nên có link đến bài trước và bài sau.
- Có thể tạo một bài index riêng cho series.

Ví dụ Front Matter:

```yaml
series:
  - Web Pentest 101
```

---

## 9. Front Matter chuẩn cho Hugo

Template Front Matter đề xuất:

```yaml
---
title: "SQL Injection Basics"
date: 2026-07-04
draft: false
categories:
  - Web Security
tags:
  - sqli
  - injection
  - web-security
summary: "A practical introduction to SQL Injection, including root cause, impact, detection, exploitation, and mitigation."
cover:
  image: "/images/sql-injection-basics-cover.png"
  alt: "SQL Injection Basics cover image"
  caption: ""
  relative: false
---
```

### Quy tắc Front Matter

- `title`: rõ nghĩa, chứa keyword chính.
- `date`: ngày publish.
- `draft`: để `true` khi chưa muốn publish.
- `categories`: chọn từ danh sách category chuẩn.
- `tags`: 3–7 tags liên quan.
- `summary`: 1–2 câu mô tả bài viết.
- `cover.image`: dùng cho ảnh ngang dạng banner.
- Logo/icon nhỏ nên chèn trong body bằng HTML thay vì dùng làm cover.

---

## 10. Quy tắc dùng hình ảnh

### Cover image

Dùng cho ảnh ngang dạng banner.

Ví dụ:

```yaml
cover:
  image: "/images/web-security-cover.png"
  alt: "Web Security cover image"
```

### Ảnh nhỏ trong nội dung

Nếu là logo, icon, sơ đồ nhỏ hoặc ảnh minh họa không phải banner, nên chèn trong body:

```html
<img src="/images/example-logo.png" alt="Example logo" width="300">
```

### Quy tắc ảnh

- Cover nên là ảnh ngang.
- Không dùng logo nhỏ làm cover.
- Luôn có `alt` text.
- Ảnh minh họa trong body nên giới hạn width nếu quá lớn.
- File ảnh nên đặt tên rõ nghĩa.

Ví dụ:

```text
/images/sql-injection-basics-cover.png
/images/burp-suite-proxy-flow.png
/images/nuclei-recon-workflow.png
```

---

## 11. Cấu trúc nội dung đề xuất cho KevinSec

```text
KevinSec Blog
├── Web Security
│   ├── HTTP Basics
│   ├── Authentication
│   ├── Access Control
│   ├── XSS
│   ├── SQL Injection
│   ├── SSRF
│   ├── CSRF
│   ├── File Upload
│   ├── API Security
│   └── JWT / OAuth
│
├── Recon
│   ├── Subdomain Enumeration
│   ├── Asset Discovery
│   ├── Fingerprinting
│   ├── Historical Data
│   └── Attack Surface Management
│
├── Red Team
│   ├── Initial Access
│   ├── Privilege Escalation
│   ├── Active Directory
│   ├── Lateral Movement
│   └── Persistence
│
├── Tools
│   ├── Burp Suite
│   ├── Nuclei
│   ├── ffuf
│   ├── Amass
│   ├── sqlmap
│   └── Custom Scripts
│
├── Writeups
│   ├── PortSwigger Labs
│   ├── HTB / CTF
│   ├── DVWA / bWAPP
│   └── Bug Bounty Case Studies
│
├── Research
│   ├── CVE Analysis
│   ├── Root Cause Analysis
│   ├── Patch Analysis
│   └── Responsible Disclosure
│
└── Career
    ├── Interview Handbook
    ├── OSCP / CPTS Preparation
    ├── Freelance Pentest
    └── Personal Branding
```

---

## 12. Checklist trước khi publish bài mới

Trước khi publish, kiểm tra nhanh:

```text
[ ] Title rõ nghĩa và có keyword chính
[ ] URL slug ngắn gọn, dễ hiểu
[ ] Category đúng danh sách chuẩn
[ ] Có 3–7 tags phù hợp
[ ] Summary rõ ràng
[ ] Cover image là ảnh ngang nếu có dùng cover
[ ] Ảnh nhỏ trong body đã giới hạn width nếu cần
[ ] Bài viết có cấu trúc heading rõ ràng
[ ] Có ít nhất 2–3 internal links nếu đã có bài liên quan
[ ] Có mục Related Articles cuối bài
[ ] Có References nếu là bài kỹ thuật hoặc research
[ ] Không publish bài nháp chưa hoàn thiện
```

---

## 13. Quy trình áp dụng nhanh

Khi viết một bài mới, chỉ cần làm theo thứ tự sau:

```text
1. Xác định Pillar Topic
2. Xác định bài này thuộc Cluster nào
3. Chọn Category
4. Chọn Tags
5. Viết URL slug
6. Dùng template bài phù hợp
7. Thêm internal links
8. Thêm Related Articles
9. Kiểm tra Front Matter
10. Publish
```

---

## 14. Nguyên tắc duy trì lâu dài

- Ưu tiên viết theo cụm chủ đề thay vì viết ngẫu nhiên.
- Mỗi tháng nên review lại category và tag một lần.
- Không tạo tag/category mới nếu không cần thiết.
- Bài viết cũ nên được update link đến bài mới.
- Một bài tốt nên có khả năng dẫn người đọc sang 2–5 bài khác.
- Blog nên phát triển như một hệ thống tri thức, không chỉ là nhật ký đăng bài.

---

## 15. Ví dụ Front Matter hoàn chỉnh

```yaml
---
title: "DOM XSS Basics for Web Pentesters"
date: 2026-07-04
draft: false
categories:
  - Web Security
tags:
  - xss
  - dom-xss
  - javascript
  - web-security
summary: "A practical introduction to DOM-based XSS for web pentesters, focusing on data flow, source, sink, root cause, and testing methodology."
cover:
  image: "/images/dom-xss-basics-cover.png"
  alt: "DOM XSS Basics cover image"
  caption: ""
  relative: false
series:
  - Web Pentest 101
---
```

---

## 16. Kết luận

Content Architecture là bộ quy chuẩn giúp KevinSec Blog phát triển có hệ thống.

Không cần xử lý như một task kỹ thuật phức tạp. Chỉ cần lưu tài liệu này trong repo và dùng nó như checklist mỗi khi viết bài mới.

Đường dẫn đề xuất trong repo:

```text
docs/content-architecture.md
```
