# Uniace Website Traffic, SEO & User Journey Analytics

### Phân tích hiệu quả Traffic, SEO Content và hành trình người dùng từ Blog/Post sang Course

**Author:** Nguyễn Thị Ngọc Bích  
**Project Type:** Data Analyst / Business Intelligence Project  
**Tools:** Excel, PostgreSQL, Power BI  
**Dataset:** Uniace Website Event Data  
**Analysis Period:** 01/01/2025 – 01/04/2025

---
## Live Dashboard

[Open Power BI Dashboard](https://app.powerbi.com/view?r=eyJrIjoiNTM2ZmRmY2QtZTM1Mi00MDk5LWJkNTYtODNhMDk5YzVlYjc2IiwidCI6IjZhYzJhZDA2LTY5MmMtNDY2My1iN2FmLWE5ZmYyYTg2NmQwYyIsImMiOjEwfQ%3D%3D)


## Bối cảnh dự án

Uniace là website giáo dục cung cấp các nội dung học tập và khóa học liên quan đến kỹ năng văn phòng, Excel, Power BI, phân tích dữ liệu và định hướng nghề nghiệp. Website sử dụng các bài viết, trang nội dung và trang khóa học để thu hút người dùng từ nhiều nguồn như Google, Cốc Cốc, Facebook, Direct, Internal và các nguồn referral khác.

Mục tiêu của phân tích không chỉ là đo lượng truy cập, mà còn đánh giá chất lượng traffic và khả năng dẫn dắt người dùng từ việc đọc Blog/Post sang tìm hiểu Course, Cart hoặc Checkout.

---

## Tóm tắt điều hành

Trong giai đoạn 01/01/2025 – 01/04/2025, website Uniace ghi nhận khoảng **30K lượt xem trang** và tiếp cận **3.817 người dùng**. Các nguồn truy cập chính đến từ Google, Direct và Internal. Google là nguồn traffic lớn nhất, cho thấy SEO đang đóng vai trò quan trọng trong việc thu hút người dùng mới.

Về nội dung, các bài viết liên quan đến **Excel, Power BI, Data Analyst, SQL** và chương trình **Young Talent Program** đang nhận được nhiều lượt quan tâm. Tuy nhiên, phân tích phễu người dùng cho thấy phần lớn user mới dừng ở Blog/Post; tỷ lệ chuyển tiếp sang Course và Cart còn thấp. Đây là điểm cần tối ưu để tăng hiệu quả marketing thực tế.

---

## Mục tiêu phân tích

Dự án tập trung trả lời các câu hỏi chính:

- Website có bao nhiêu users và pageviews trong giai đoạn phân tích?
- Nguồn traffic nào mang lại nhiều người dùng nhất?
- Nguồn nào có tiềm năng tạo ra users quan tâm khóa học?
- Nội dung nào được xem nhiều nhất?
- Trang khóa học nào được quan tâm nhiều nhất?
- SEO có đóng góp đáng kể vào traffic không?
- Người dùng có đi từ Blog/Post sang Course không?
- Website nên tối ưu nội dung và trang khóa học như thế nào để cải thiện chuyển đổi?

---

## Quy trình xử lý dữ liệu

### 1. Import dữ liệu

Dữ liệu ban đầu được lưu ở dạng Excel và được import vào PostgreSQL để xử lý. PostgreSQL được sử dụng để làm sạch dữ liệu, phân loại nguồn truy cập, phân loại URL và chuẩn bị dữ liệu cho dashboard Power BI.

### 2. Làm sạch dữ liệu

Các bước xử lý chính:

- Chuẩn hóa kiểu dữ liệu ngày giờ.
- Lọc các event liên quan đến pageview.
- Xử lý giá trị null trong URL và Referrer.
- Chuẩn hóa URL/path để phân loại trang.
- Loại bỏ hoặc kiểm tra các dòng trùng lặp nếu có.

### 3. Tạo các trường phân tích

Các trường được tạo thêm để phục vụ dashboard:

| Field | Ý nghĩa |
|---|---|
| `source` | Nguồn truy cập: Google, Cốc Cốc, Facebook, Direct, Internal, Email, Other Referral |
| `url_type` | Loại trang: Blog/Post, Course, Cart, Checkout, Home, Account, Other |
| `is_seo` | Đánh dấu traffic từ search engine như Google, Cốc Cốc, Bing, Yahoo |
| `is_blog_post` | Đánh dấu các trang nội dung hoặc bài viết |
| `is_course` | Đánh dấu các trang khóa học |
| `is_cart` | Đánh dấu trang giỏ hàng |
| `date_only` | Ngày dùng để phân tích theo thời gian |

---

## Logic phân loại dữ liệu

### Phân loại Source

`source` được tạo từ cột Referrer.

```sql
CASE
    WHEN ma_referrer IS NULL OR ma_referrer = '' THEN 'Direct / Unknown'
    WHEN LOWER(ma_referrer) LIKE '%google%' THEN 'Google'
    WHEN LOWER(ma_referrer) LIKE '%coccoc%' THEN 'Cốc Cốc'
    WHEN LOWER(ma_referrer) LIKE '%bing%' THEN 'Bing'
    WHEN LOWER(ma_referrer) LIKE '%yahoo%' THEN 'Yahoo'
    WHEN LOWER(ma_referrer) LIKE '%facebook%' OR LOWER(ma_referrer) LIKE '%fb.com%' THEN 'Facebook'
    WHEN LOWER(ma_referrer) LIKE '%youtube%' THEN 'YouTube'
    WHEN LOWER(ma_referrer) LIKE '%uniace.vn%' THEN 'Internal'
    WHEN LOWER(ma_referrer) LIKE '%sendinblue%' THEN 'Email'
    ELSE 'Other Referral'
END AS source
```

### Phân loại URL Type

`url_type` được tạo từ URL path.

```sql
CASE
    WHEN ma_path = '/' THEN 'Home'
    WHEN ma_path = '/blog/' THEN 'Blog Listing'
    WHEN ma_path LIKE '%/course/%' THEN 'Course'
    WHEN ma_path LIKE '%/shop%' OR ma_path LIKE '%/product-category%' THEN 'Course'
    WHEN ma_path LIKE '%/cart%' THEN 'Cart'
    WHEN ma_path LIKE '%/checkout%' THEN 'Checkout'
    WHEN ma_path LIKE '%/my-account%' THEN 'Account'
    WHEN ma_path LIKE '%/contact%' OR ma_path LIKE '%/lien-he%' THEN 'Contact'
    ELSE 'Blog/Post'
END AS url_type
```

---

## Data Model đề xuất

Dự án có thể được tổ chức theo mô hình phân tích đơn giản gồm bảng fact và các bảng hỗ trợ.

### Fact table

#### `fact_web_events`

Mỗi dòng đại diện cho một event/pageview trên website.

Các trường chính:

- `event_date`
- `date_only`
- `cuid`
- `ip`
- `ma_url`
- `ma_path`
- `ma_referrer`
- `source`
- `url_type`
- `is_seo`
- `is_blog_post`
- `is_course`
- `is_cart`
- `event_type`

### Summary tables

#### `url_performance_summary`

Dùng để phân tích hiệu quả từng URL.

| Metric | Ý nghĩa |
|---|---|
| Pageviews | Tổng lượt xem trang |
| Users | Số người dùng truy cập URL |
| SEO Users | Số users đến từ search engine |
| Course Users | Số users có vào trang khóa học |
| Course Rate | Tỷ lệ users chuyển sang Course |
| SEO Share | Tỷ trọng users đến từ SEO |

#### `user_journey_summary`

Dùng để phân tích hành trình người dùng.

| Field | Ý nghĩa |
|---|---|
| User Path | Luồng truy cập phổ biến |
| First Source | Nguồn truy cập đầu tiên |
| Has Blog/Post | Có xem Blog/Post hay không |
| Has Course | Có xem Course hay không |
| Has Cart | Có vào Cart hay không |
| Journey Segment | Nhóm hành vi người dùng |

---

## Các chỉ số chính

| Metric | Công thức / Cách hiểu |
|---|---|
| Total Users | Số người dùng duy nhất |
| Total Pageviews | Tổng số lượt xem trang |
| SEO Users | Users đến từ Google, Cốc Cốc, Bing, Yahoo |
| Blog/Post Users | Users có xem trang nội dung |
| Course Users | Users có xem trang khóa học |
| Cart Users | Users có đi tới Cart |
| Pageviews/User | Total Pageviews / Total Users |
| SEO Share | SEO Users / Total Users |
| Course Interest Rate | Course Users / Total Users |
| Blog/Post → Course Rate | Users có Blog/Post và Course / Blog/Post Users |
| Course → Cart Rate | Users có Course và Cart / Course Users |

---

## Dashboard Design

Dashboard được xây dựng trên Power BI và chia thành các trang chính.

### 1. Overview

Mục tiêu: cung cấp góc nhìn tổng quan về traffic, users và nguồn truy cập.

Các thành phần chính:

- KPI: Total Users, Total Pageviews, SEO Users, Course Users.
- Line chart: Pageviews by Day.
- Bar chart: Users by Traffic Source.
- Top Content Pages.
- Top Course Pages.
- Top Pages from SEO Traffic.

### 2. SEO & Content Performance

Mục tiêu: đánh giá hiệu quả SEO và nội dung website.

Các thành phần chính:

- SEO Users.
- SEO Share.
- Top Pages from SEO Traffic.
- Top Content Pages by Pageviews.
- Content to Course Performance.

Ý nghĩa: xác định bài viết nào không chỉ kéo traffic tốt mà còn có khả năng dẫn người dùng sang trang khóa học.

### 3. User Journey & Funnel

Mục tiêu: phân tích hành vi người dùng và các điểm rơi trong hành trình.

Funnel chính:

```text
Total Users → Blog/Post Users → Course Users → Cart Users
```

Ví dụ User Path:

```text
Google → Blog/Post → Exit
Google → Blog/Post → Course
Google → Blog/Post → Course → Cart
Direct → Course → Cart
```

---

## Kết quả phân tích chính

### 1. Tổng quan traffic

Website đạt khoảng **30K lượt xem trang** và tiếp cận **3.817 người dùng** trong giai đoạn phân tích. Điều này cho thấy website có khả năng thu hút traffic ổn định.

Traffic tăng mạnh vào giai đoạn tháng 1 và tháng 2. Đây có thể là thời điểm người dùng có nhu cầu học thêm kỹ năng, chuẩn bị cho kế hoạch học tập hoặc công việc sau Tết.

### 2. Nguồn truy cập

Google là nguồn mang lại nhiều người dùng nhất, cho thấy SEO là kênh thu hút traffic quan trọng. Direct đứng thứ hai, phản ánh website đã có một lượng người dùng nhất định biết đến thương hiệu hoặc chủ động quay lại truy cập.

Internal cũng chiếm tỷ trọng đáng kể, cho thấy người dùng có xu hướng di chuyển giữa các trang trong website.

### 3. SEO & Content

Các bài viết liên quan đến Excel, Power BI, Data Analyst và SQL nằm trong nhóm được xem nhiều nhất. Điều này cho thấy hướng phát triển nội dung về Data đang phù hợp với nhu cầu người dùng.

Tuy nhiên, một số content pages có traffic cao nhưng Course Rate thấp. Đây là nhóm cần được tối ưu để tăng khả năng dẫn người đọc sang trang khóa học.

### 4. User Journey & Funnel

Website có khoảng **14K users**, trong đó khoảng **12K users** xem Blog/Post. Tuy nhiên, chỉ khoảng **1.43K users** vào Course và số Cart Users rất thấp. Điều này cho thấy content đang thu hút người đọc tốt, nhưng khả năng chuyển từ Blog/Post sang Course còn hạn chế.

Điểm rơi lớn nhất nằm ở bước Blog/Post → Course và Course → Cart.

---

## Insight chính

- SEO và Content là hai hướng phân tích trọng tâm của dashboard.
- Google là nguồn traffic lớn nhất và có vai trò quan trọng trong việc thu hút người dùng mới.
- Direct cao cho thấy Uniace đã có một lượng người dùng biết đến thương hiệu hoặc chủ động quay lại.
- Các chủ đề Data, Excel, Power BI và Young Talent Program đang có tín hiệu tốt.
- Nhiều người dùng đọc Blog/Post nhưng chưa đi tiếp sang Course.
- Trang Course cần rõ ràng hơn để hỗ trợ người dùng ra quyết định.

---

## Giải pháp đề xuất

### 1. Nâng cao chất lượng nội dung

Tập trung viết các bài thực tế hơn về Data, Excel, Power BI, SQL, lộ trình học và kinh nghiệm ứng tuyển. Nội dung nên giúp người đọc giải quyết vấn đề thật, từ đó xây dựng niềm tin với Uniace.

### 2. Tối ưu các bài viết có traffic cao nhưng Course Rate thấp

Chọn các bài có nhiều lượt đọc nhưng ít người vào Course để tối ưu trước. Có thể thêm CTA rõ ràng, banner khóa học liên quan và internal link đến các trang Course phù hợp.

### 3. Cải thiện trang khóa học

Trang khóa học cần trả lời rõ các câu hỏi: học gì, học trong bao lâu, phù hợp với ai, học xong làm được gì, có dự án thực hành không, học phí bao nhiêu và đăng ký ở đâu.

### 4. Tận dụng các chủ đề đang có tín hiệu tốt

Tiếp tục phát triển nội dung xoay quanh Excel, Power BI, Data Analyst, SQL và Young Talent Program. Đây là nhóm chủ đề có khả năng tiếp cận người dùng đang quan tâm đến học kỹ năng và tìm kiếm cơ hội nghề nghiệp.

### 5. Theo dõi traffic theo chất lượng

Không chỉ đánh giá nguồn nào có nhiều users, mà cần theo dõi thêm Course Users và Course Rate theo từng source để xác định kênh nào mang lại người dùng có tiềm năng chuyển đổi.

---

## Giá trị đạt được

Thông qua dự án, người thực hiện đã rèn luyện được các kỹ năng:

- Làm sạch và chuẩn hóa dữ liệu website event.
- Xử lý dữ liệu bằng PostgreSQL.
- Thiết kế các trường phân loại source, URL type và user journey.
- Xây dựng dashboard Power BI theo hướng business insight.
- Phân tích traffic, SEO, content marketing và funnel người dùng.
- Đưa ra đề xuất thực tế dựa trên dữ liệu.

---

## Công nghệ sử dụng

| Công cụ | Vai trò |
|---|---|
| Excel | Nguồn dữ liệu ban đầu |
| PostgreSQL | Làm sạch, biến đổi và tổng hợp dữ liệu |
| Power BI | Xây dựng dashboard và trực quan hóa |
| DAX | Tạo measure phân tích |
| SQL | Truy vấn, phân loại và tạo bảng summary |

---

## Cấu trúc repo đề xuất

```text
uniace-website-seo-user-journey-analysis/
│
├── README.md
├── README_VN.md
│
├── data/
│   └── raw_data_sample.xlsx
│
├── sql/
│   ├── 01_create_tables.sql
│   ├── 02_clean_transform.sql
│   ├── 03_create_summary_tables.sql
│   └── 04_analysis_queries.sql
│
├── powerbi/
│   └── Uniace_Website_Analytics.pbix
│
├── report/
│   └── Uniace_SEO_Marketing_Report.pdf
│
└── images/
    ├── dashboard_overview.png
    ├── seo_content_page.png
    └── user_journey_funnel.png
```

---

## Liên hệ

Mọi thắc mắc, phản hồi hoặc góp ý liên quan đến bài report xin vui lòng liên hệ qua email:

**nguyenthingocbich552003@gmail.com**
