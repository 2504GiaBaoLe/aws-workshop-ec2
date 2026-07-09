---
title: "Worklog Tuần 9"
date: 2024-01-01
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---


### Mục tiêu tuần 9:

* Tìm hiểu các dịch vụ mạng nâng cao trên AWS.
* Hiểu cách kết nối nhiều VPC với nhau.
* Tìm hiểu khái niệm AWS Direct Connect.
* Tìm hiểu Amazon CloudFront và cách hoạt động của CDN.
* Thực hành xây dựng mô hình ứng dụng có thể phục vụ người dùng ở nhiều khu vực.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Tìm hiểu VPC Peering <br> - Tìm hiểu AWS Transit Gateway <br> - So sánh hai phương pháp kết nối nhiều VPC | 15/06/2026 | 15/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Tìm hiểu AWS Direct Connect <br> - Hiểu mục đích và các trường hợp sử dụng Direct Connect | 16/06/2026 | 16/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - Tìm hiểu Amazon CloudFront <br>&emsp; + Distribution <br>&emsp; + Edge Location <br>&emsp; + Cache Behavior | 17/06/2026 | 17/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - Thực hành tạo CloudFront Distribution <br> - Kết nối CloudFront với S3 hoặc Web Server | 18/06/2026 | 18/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Thực hành:** <br>&emsp; + Tuần này không làm thực hành | 19/06/2026 | 19/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 9:

* Hiểu được mục đích của các dịch vụ Networking nâng cao trên AWS và khi nào nên sử dụng chúng.

* Tìm hiểu VPC Peering và biết cách kết nối hai VPC để các tài nguyên có thể giao tiếp với nhau.

* Hiểu sự khác nhau giữa VPC Peering và AWS Transit Gateway, đồng thời biết được Transit Gateway phù hợp hơn khi cần kết nối nhiều VPC.

* Tìm hiểu AWS Direct Connect và hiểu đây là dịch vụ giúp kết nối mạng nội bộ (On-premises) với AWS thông qua đường truyền riêng.

* Hiểu rằng Direct Connect thường được doanh nghiệp sử dụng khi cần đường truyền ổn định, độ trễ thấp và băng thông cao.

* Tìm hiểu Amazon CloudFront và biết đây là dịch vụ CDN giúp phân phối nội dung đến người dùng thông qua các Edge Location.

* Quan sát cách CloudFront lưu nội dung vào bộ nhớ đệm (Cache) nhằm giảm thời gian tải dữ liệu và giảm tải cho máy chủ gốc.

* Sau khi hoàn thành các bài thực hành, tôi hiểu rõ hơn vai trò của từng dịch vụ trong việc tối ưu kết nối mạng và cải thiện tốc độ truy cập của ứng dụng trên AWS.