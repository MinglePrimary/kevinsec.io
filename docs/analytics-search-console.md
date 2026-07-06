# Analytics và Search Console

Tài liệu này ghi lại quy trình cấu hình Google Analytics 4 và Google Search Console cho website Hugo/PaperMod `kevinsec.io`.

## 1. Mục tiêu

- Theo dõi traffic website bằng Google Analytics 4.
- Xác minh website trong Google Search Console.
- Submit sitemap để Google phát hiện URL.
- Theo dõi tình trạng index, crawl và hiệu suất tìm kiếm.
- Có checklist kiểm tra sau mỗi lần publish bài mới.

---

## 2. Công cụ sử dụng

| Công cụ | Mục đích |
|---|---|
| Google Analytics 4 | Theo dõi user, session, page view, nguồn traffic |
| Google Search Console | Theo dõi indexing, sitemap, query, click, impression |
| Hugo/PaperMod | Sinh static site, sitemap, robots.txt |
| GitHub Pages | Deploy website production |

---

## 3. Cấu hình Google Analytics 4

### 3.1 Tạo GA4 Property

Truy cập Google Analytics:

```txt
https://analytics.google.com/
```

Tạo property mới:

```txt
Admin → Create → Property
```

Thông tin đề xuất:

| Field | Giá trị |
|---|---|
| Property name | KevinSec |
| Time zone | Vietnam |
| Platform | Web |
| Website URL | https://kevinsec.io |
| Stream name | KevinSec Website |

Sau khi tạo xong, lưu lại Measurement ID dạng:

```txt
G-XXXXXXXXXX
```

Ví dụ hiện tại:

```txt
G-XHWBGBB6JP
```

---

## 4. Gắn Google Analytics vào Hugo

Mở file cấu hình Hugo ở root project:

```txt
hugo.toml
```

Thêm hoặc kiểm tra các dòng sau:

```toml
baseURL = "https://kevinsec.io/"
languageCode = "en-us"
title = "KevinSec"
theme = "PaperMod"
enableRobotsTXT = true
googleAnalytics = "G-XHWBGBB6JP"
```

Sau đó build thử local:

```bash
hugo --minify
```

Nếu không có lỗi, commit và deploy:

```bash
git status
git add hugo.toml
git commit -m "Add analytics and robots configuration"
git push
```

---

## 5. Kiểm tra Google Analytics hoạt động

Sau khi GitHub Actions deploy thành công, mở website production:

```txt
https://kevinsec.io/
```

Kiểm tra source HTML:

```txt
Right click → View Page Source → Ctrl + F
```

Tìm một trong hai chuỗi:

```txt
G-XHWBGBB6JP
googletagmanager
```

Nếu tìm thấy, GA4 script đã được nhúng vào website.

Tiếp theo vào Google Analytics:

```txt
Reports → Realtime
```

Mở website ở tab khác và duyệt vài trang:

```txt
https://kevinsec.io/
https://kevinsec.io/blog/
https://kevinsec.io/research/
```

Kỳ vọng thấy ít nhất một active user hoặc page view realtime.

---

## 6. Cấu hình Google Search Console

Truy cập:

```txt
https://search.google.com/search-console/
```

Thêm property mới:

```txt
Add property → Domain
```

Nhập domain:

```txt
kevinsec.io
```

Nên dùng Domain Property vì nó bao phủ cả:

```txt
https://kevinsec.io/
http://kevinsec.io/
https://www.kevinsec.io/
http://www.kevinsec.io/
```

---

## 7. Verify ownership bằng DNS TXT

Google Search Console sẽ cung cấp TXT record dạng:

```txt
google-site-verification=xxxxxxxxxxxxxxxx
```

Vào DNS provider của domain `kevinsec.io`, thêm record:

| Type | Name/Host | Value | TTL |
|---|---|---|---|
| TXT | @ hoặc kevinsec.io | google-site-verification=... | Default hoặc 1 Day |

Sau đó quay lại Google Search Console và bấm:

```txt
Verify
```

Kết quả hiện tại: website đã xác minh quyền sở hữu thành công.

Lưu ý: Không xóa TXT record sau khi verify để tránh mất trạng thái xác minh trong tương lai.

---

## 8. Kiểm tra robots.txt và sitemap.xml

Kiểm tra hai URL sau trên browser:

```txt
https://kevinsec.io/robots.txt
https://kevinsec.io/sitemap.xml
```

`robots.txt` nên có dạng:

```txt
User-agent: *
Allow: /

Sitemap: https://kevinsec.io/sitemap.xml
```

`sitemap.xml` cần mở được và chứa danh sách URL của website.

---

## 9. Submit sitemap trong Search Console

Trong Google Search Console, chọn property `kevinsec.io`.

Vào:

```txt
Lập chỉ mục → Sơ đồ trang web
```

Nhập sitemap:

```txt
sitemap.xml
```

Hoặc URL đầy đủ:

```txt
https://kevinsec.io/sitemap.xml
```

Bấm:

```txt
Gửi
```

Kết quả hiện tại:

| Trạng thái | Kết quả |
|---|---|
| Sitemap URL | https://kevinsec.io/sitemap.xml |
| Status | Thành công |
| Last read | 6/7/2026 |
| Pages discovered | 23 |

---

## 10. Request indexing các trang quan trọng

Dùng thanh kiểm tra URL ở đầu Google Search Console:

```txt
Kiểm tra mọi URL trong "kevinsec.io"
```

Kiểm tra lần lượt các URL chính:

```txt
https://kevinsec.io/
https://kevinsec.io/blog/
https://kevinsec.io/research/
https://kevinsec.io/services/
https://kevinsec.io/about/
https://kevinsec.io/contact/
```

Với mỗi URL:

```txt
URL Inspection → Test Live URL → Request Indexing
```

Không cần request indexing toàn bộ website. Chỉ ưu tiên:

- Home page.
- Blog listing.
- Research listing.
- Services/About/Contact.
- Các bài blog hoặc research quan trọng.

---

## 11. Checklist sau mỗi lần publish bài mới

Sau khi publish bài mới qua Decap CMS hoặc Git:

```txt
[ ] Bài viết mở được trên production
[ ] URL đúng slug mong muốn
[ ] Title hiển thị đúng
[ ] Description/front matter đầy đủ
[ ] Cover image hoặc thumbnail hiển thị đúng
[ ] Bài viết xuất hiện ở Blog hoặc Research listing
[ ] Bài viết có trong sitemap.xml
[ ] Không bị chặn bởi robots.txt
[ ] Có internal link phù hợp từ bài khác hoặc trang listing
[ ] Kiểm tra URL trong Search Console
[ ] Request indexing nếu bài quan trọng
```

---

## 12. Checklist theo dõi hằng tuần

Mỗi tuần kiểm tra Google Search Console:

```txt
[ ] Performance: clicks, impressions, CTR, average position
[ ] Queries: người dùng tìm từ khóa gì để thấy website
[ ] Pages: trang nào đang có impression/click
[ ] Sitemaps: sitemap còn trạng thái Success không
[ ] Pages indexing: trang nào bị not indexed
[ ] 404: có URL lỗi không
[ ] robots.txt: có trang nào bị chặn nhầm không
```

Mỗi tuần kiểm tra Google Analytics:

```txt
[ ] Users
[ ] Sessions
[ ] Views
[ ] Top pages
[ ] Traffic source
[ ] Realtime có hoạt động không
```

---

## 13. Các trạng thái Search Console cần hiểu

| Trạng thái | Ý nghĩa | Hành động |
|---|---|---|
| Indexed | Trang đã được Google index | Không cần xử lý |
| Discovered - currently not indexed | Google biết URL nhưng chưa crawl | Chờ thêm, cải thiện internal link |
| Crawled - currently not indexed | Google đã crawl nhưng chưa index | Kiểm tra chất lượng nội dung, title, duplicate content |
| Not found 404 | URL không tồn tại | Sửa link hoặc tạo redirect nếu cần |
| Blocked by robots.txt | URL bị robots chặn | Kiểm tra robots.txt |
| Duplicate without user-selected canonical | Google nghi ngờ trùng lặp | Kiểm tra canonical, title, nội dung |

---

## 14. Chỉ số nên theo dõi cho KevinSec

Nhóm chỉ số Search Console:

| Chỉ số | Ý nghĩa |
|---|---|
| Clicks | Số lượt click từ Google Search |
| Impressions | Số lần website xuất hiện trên kết quả tìm kiếm |
| CTR | Tỷ lệ click/impression |
| Average position | Vị trí trung bình trên Google |
| Queries | Từ khóa người dùng tìm kiếm |
| Pages | URL đang có hiệu suất tìm kiếm |

Nhóm chỉ số Analytics:

| Chỉ số | Ý nghĩa |
|---|---|
| Users | Số người dùng truy cập |
| Sessions | Số phiên truy cập |
| Views | Số lượt xem trang |
| Traffic source | Nguồn traffic: search, direct, social, referral |
| Top pages | Trang được xem nhiều nhất |

---

## 15. Keyword nhóm nên quan sát

Các nhóm query kỳ vọng trong tương lai:

```txt
kevinsec
kevinsec.io
web pentest
security research
vulnerability assessment
red team
application security
bug bounty writeup
cybersecurity blog
```

Với blog mới, giai đoạn đầu có thể chưa có nhiều click. Chỉ số quan trọng ban đầu là:

```txt
Impressions tăng dần
Pages được index tăng dần
Không có lỗi crawl nghiêm trọng
Một số query brand bắt đầu xuất hiện
```

---

## 16. Tiêu chí hoàn thành Lesson 18

Lesson 18 được xem là hoàn thành khi đạt các mục sau:

```txt
[x] Đã tạo Google Analytics 4 property
[x] Đã có Measurement ID
[x] Đã thêm googleAnalytics vào hugo.toml
[x] Đã bật enableRobotsTXT
[x] Đã xác minh Search Console bằng DNS TXT
[x] Đã submit sitemap.xml
[x] Sitemap có trạng thái Success
[x] Google phát hiện URL trong sitemap
[ ] Đã kiểm tra GA4 Realtime
[ ] Đã request indexing các trang chính
[ ] Đã theo dõi Search Console sau 24-48 giờ
```

---

## 17. Ghi chú vận hành

- Không xóa DNS TXT verification record.
- Không chặn `/` trong `robots.txt`.
- Không submit sitemap nhiều lần không cần thiết.
- Sau mỗi lần publish bài mới, chỉ cần kiểm tra bài có trong sitemap và request indexing nếu bài quan trọng.
- Search Console thường cần vài ngày để có dữ liệu meaningful.
- Analytics realtime có thể bị ảnh hưởng bởi ad blocker, browser privacy mode hoặc cache.
