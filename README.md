# Uniace Website Traffic,SEO & User Journey Analytics

**Author:** Nguyễn Thị Ngọc Bích  
**Project Type:** Data Analyst / Business Intelligence Project  
**Tools:** Excel, PostgreSQL, Power BI  
**Dataset:** Uniace Website Event Data  
**Analysis Period:** 01/01/2025 – 01/04/2025

## Project Overview

### Business Context
Uniace là website giáo dục cung cấp nội dung học tập và khóa học về Excel, Power BI, phân tích dữ liệu, kỹ năng văn phòng và định hướng nghề nghiệp.
Bài toán đặt ra là không chỉ theo dõi số lượt truy cập, mà còn đánh giá **traffic có chất lượng hay không**, nội dung SEO nào đang thu hút người dùng và người dùng có tiếp tục đi từ Blog/Post sang Course hoặc Cart hay không.
Marketing team cần theo dõi:

- Website đang thu hút bao nhiêu users
- Traffic đến từ nguồn nào
- SEO có hiệu quả hay không
- Content nào đang hoạt động tốt
- Người dùng có chuyển từ Blog/Post sang Course không
- Bước nào user drop nhiều nhất trong funnel

Tuy nhiên dữ liệu website đang ở dạng raw event data nên khó theo dõi trực tiếp.

Dự án được xây dựng nhằm:

- Chuẩn hóa dữ liệu website event
- Xây dựng data model phục vụ phân tích
- Thiết kế dashboard Power BI hỗ trợ business monitoring
- Phân tích SEO traffic và user journey
- Đưa ra business insights và recommendations


## Project Objectives

Dashboard được xây dựng để trả lời các câu hỏi:

- Website có bao nhiêu users và pageviews?
- Nguồn traffic nào hiệu quả nhất?
- SEO đóng góp bao nhiêu traffic?
- Content nào thu hút người dùng nhiều nhất?
- Người dùng có đi từ Blog/Post sang Course không?
- Bước nào user drop nhiều nhất trong funnel?
- Kênh nào mang lại user có khả năng chuyển đổi cao?


## End-to-End Data Flow

Để xây dựng dashboard phân tích traffic và hành trình người dùng, dữ liệu được xử lý theo pipeline sau:

```text
→ Raw excel data
→ PostgreSQL Import
→ Data Cleaning
→ Data Transformation
→ Power BI Dashboard
```
![alt text](Picture/Flow_data.png)
## Data Pipeline Overview

### Step 1 — Raw Data

Nguồn dữ liệu website event được lưu dưới dạng Excel raw data.

Các bước thực hiện:

- Kiểm tra cấu trúc file dữ liệu
- Kiểm tra kiểu dữ liệu
- Kiểm tra số dòng dữ liệu
- Kiểm tra missing values

![alt text](Picture/raw_data.png)

---

### Step 2 — Import to PostgreSQL

Dữ liệu được import vào PostgreSQL thông qua DBeaver để phục vụ xử lý và transformation.

Các bước thực hiện:

- Import raw data
- Tạo staging table
- Kiểm tra dữ liệu import
- Chuẩn bị dữ liệu cho cleaning

![alt text](<Picture/Ảnh chụp màn hình 2026-05-27 203831.png>)

---

### Step 3 — Data Cleaning

Dữ liệu được làm sạch trước khi đưa vào Power BI.

Các bước xử lý chính:

- Chuẩn hóa datetime
- Xử lý dữ liệu thiếu
- Lọc valid pageview events
- Loại bỏ duplicate rows
- Chuẩn hóa URL/path
- Kiểm tra dữ liệu bất thường

Kiểm tra Top Content đúng: chỉ Blog/Post, không có Account/Course/Home

![alt text](<Picture/Ảnh chụp màn hình 2026-05-27 205817.png>)
### Step 4 — Data Transformation

Các trường phân tích được tạo thêm để phục vụ dashboard và business analysis.

Các logic chính:

- Phân loại traffic source
- Phân loại URL type
- Xác định SEO traffic
- Xác định Blog/Post pages
- Xác định Course pages
- Xác định Cart pages

> Phân loại nguồn truy cập website như Google, Facebook, Direct, Internal, Social và Other để phục vụ phân tích hiệu quả traffic.

![Phân loại Traffic Source](<Picture/Ảnh chụp màn hình 2026-05-27 210213.png>)

---

> Phân loại URL thành các nhóm như Blog/Post, Course, Cart, Checkout, Landing Page và Technical để hỗ trợ phân tích hành vi người dùng.

![Phân loại URL Type](<Picture/Ảnh chụp màn hình 2026-05-27 210334.png>)

---

### Step 5 — Power BI Dashboard

Sau khi xử lý dữ liệu, dashboard được xây dựng trên Power BI để:

- Theo dõi traffic website
- Đánh giá hiệu quả SEO
- Phân tích content performance
- Phân tích user journey
- Theo dõi funnel conversion
 Theo dõi traffic website 

![alt text](<Picture/Ảnh chụp màn hình 2026-05-27 210906.png>)


## Data Dictionary

Chi tiết về cấu trúc dữ liệu, định nghĩa metrics, dimensions và các bảng được sử dụng trong project có thể xem tại link bên dưới:

📄 [View Detailed Data Dictionary](Uniace_Data_Dictionary.xlsx)


![alt text](<Picture/03_48_10 PM.png>)
---

## Data Model

Dữ liệu sau xử lý được thiết kế theo mô hình phân tích dạng Fact - Dimension, giúp tổ chức dữ liệu rõ ràng và thuận tiện cho việc xây dựng báo cáo trong Power BI.

Mô hình gồm 4 bảng chính:

- `public_fact_events`: bảng fact chính, lưu toàn bộ sự kiện người dùng trên website.
- `public_dim_user`: bảng dimension lưu thông tin người dùng.
- `public_dim_page`: bảng dimension lưu thông tin trang và phân loại URL.
- `public_dim_source`: bảng dimension lưu thông tin nguồn traffic.

Mô hình này giúp:

- Liên kết dữ liệu event với người dùng, trang truy cập và nguồn traffic.
- Tối ưu việc tính toán KPI bằng DAX.
- Hỗ trợ phân tích traffic source, SEO performance và user journey.
- Giúp dashboard hoạt động rõ ràng, dễ mở rộng và dễ kiểm tra logic dữ liệu.

![alt text](<Picture/03_48_10 PM.png>)

## Main Tables

### fact_web_events

Fact table chứa toàn bộ website events.

### url_performance_summary

Summary table phục vụ phân tích hiệu quả từng URL.

### user_journey_summary

Summary table phục vụ phân tích hành trình người dùng.

![alt text](<Picture/Ảnh chụp màn hình 2026-05-27 211433.png>)


## KPI Definitions

| KPI | Formula | Meaning |
|---|---|---|
| Total Users | DISTINCTCOUNT(cuid) | Số user duy nhất |
| Total Pageviews | COUNTROWS(events) | Tổng lượt xem |
| SEO Users | Users from SEO traffic | User từ search engine |
| Blog/Post Users | Users visited blog pages | User đọc content |
| Course Users | Users visited course pages | User quan tâm khóa học |
| Cart Users | Users visited cart page | User có intent chuyển đổi |
| SEO Share | SEO Users / Total Users | Tỷ trọng SEO |
| Course Rate | Course Users / Total Users | Tỷ lệ quan tâm khóa học |
| Blog → Course Rate | Blog & Course Users / Blog Users | Hiệu quả content |

![alt text](<Picture/Ảnh chụp màn hình 2026-05-27 214538.png>)

## Dashboard Design

### 1. Overview Dashboard 

### Objective

Theo dõi tổng quan traffic website, nguồn truy cập, hiệu quả SEO và hiệu suất content marketing.

### Main Visuals

- KPI Cards
- Traffic Trend
- Users by Source
- SEO Traffic Overview
- SEO Users / SEO Share
- Top Content Pages
- Top SEO Pages
- Top Course Pages
- Content to Course Performance

### Business Value

Dashboard này giúp marketing team:

- Theo dõi traffic tổng thể của website
- Đánh giá hiệu quả SEO theo nguồn truy cập và trang đích
- Xác định các content/page mang lại nhiều traffic nhất
- Phân tích nội dung nào có khả năng dẫn user sang course
- Theo dõi hành vi người dùng từ content đến course page

📌 Dashboard Preview

![Traffic SEO Content Overview](<Picture/Ảnh chụp màn hình 2026-05-27 211552.png>)



## 3. User Journey & Funnel

### Objective

Phân tích hành trình người dùng từ Blog/Post đến Course và Cart.

### Funnel Structure

```text
Total Users
→ Blog/Post Users
→ Course Users
→ Cart Users
```

### Business Value

Giúp xác định:

- User drop ở bước nào
- Content có hỗ trợ chuyển đổi không
- Funnel conversion có hiệu quả không

![alt text](<Picture/Ảnh chụp màn hình 2026-05-27 211954.png>)


## Key Insights

### Traffic Insights

- Google là nguồn traffic lớn nhất
- SEO đóng vai trò quan trọng trong user acquisition
- Direct traffic cao cho thấy đã có user quay lại website
- Internal traffic cho thấy user có xu hướng khám phá nhiều trang


## Content Insights

Các chủ đề có performance tốt:

- Excel
- Power BI
- SQL
- Data Analyst
- Young Talent Program

Tuy nhiên nhiều content pages có traffic cao nhưng Course Rate thấp.

Điều này cho thấy:

- Content đang thu hút user tốt
- Nhưng CTA và internal linking chưa tối ưu


## Funnel Insights

Phần lớn user dừng ở bước Blog/Post.

Điểm drop lớn nhất nằm ở:

```text
Blog/Post → Course
Course → Cart
```

Điều này cho thấy cần tối ưu:

- CTA
- Landing page
- Course content presentation
- Internal linking


## Recommendations

### 1. Improve CTA in Blog Content

Thêm CTA rõ ràng trong các bài viết có traffic cao.

### 2. Improve Internal Linking

Liên kết các bài blog với các course liên quan.


### 3. Optimize Course Landing Pages

Course page cần trả lời rõ:

- Học gì
- Phù hợp với ai
- Học xong làm được gì
- Có project thực hành không
- Học phí và đăng ký như thế nào

### 4. Focus on High-Performing Topics

Tiếp tục phát triển content về:

- Excel
- SQL
- Power BI
- Data Analyst
- Career roadmap


### 5. Track Quality Traffic

Không chỉ theo dõi pageviews mà cần theo dõi:

- Course Users
- Course Rate
- Funnel conversion
- SEO quality


# Skills Demonstrated

Thông qua dự án, người thực hiện đã rèn luyện được:

- SQL Data Cleaning
- PostgreSQL Transformation
- Power BI Dashboard Design
- DAX KPI Calculation
- User Journey Analysis
- SEO & Content Analysis
- Business Insight Presentation
- Data Storytelling


# Tech Stack

| Tool | Usage |
|---|---|
| Excel | Raw dataset |
| PostgreSQL | Data cleaning & transformation |
| SQL | Query & processing |
| Power BI | Dashboard & visualization |
| DAX | KPI calculation |
| DBeaver | Database management |


# Repository Structure

```text
uniace-website-seo-user-journey-analysis/
│
├── README.md
├── data/
│   └── raw_data_sample.xlsx
│
├── sql/
│
├── powerbi/
│   └── Uniace_Website_Analytics.pbix
│
├── report/
│   └── Uniace_Analytics_Report.pdf
│
└── images/
```

---

# Contact

**Nguyễn Thị Ngọc Bích**  
Data Analyst / Business Intelligence  

📧 Email: nguyenthingocbich552003@gmail.com

