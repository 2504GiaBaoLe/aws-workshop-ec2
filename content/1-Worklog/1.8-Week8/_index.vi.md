---
title: "Worklog Tuần 8"
date: 2024-01-01
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---


### Mục tiêu tuần 8:

* Tìm hiểu các dịch vụ giám sát và ghi log trên AWS.
* Hiểu cách Amazon CloudWatch theo dõi tài nguyên và ứng dụng.
* Tìm hiểu AWS CloudTrail để ghi nhận và kiểm tra các hoạt động trên tài khoản AWS.
* Tìm hiểu AWS X-Ray để theo dõi luồng xử lý và phân tích hiệu năng ứng dụng.
* Thực hành xây dựng một hệ thống giám sát toàn diện bằng các dịch vụ Monitoring & Logging của AWS.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Tìm hiểu tổng quan về Monitoring & Logging trên AWS <br> - Tìm hiểu các khái niệm Monitoring, Logging và Observability | 08/06/2026 | 08/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Tìm hiểu Amazon CloudWatch <br>&emsp; + Metrics <br>&emsp; + Logs <br>&emsp; + Alarms <br>&emsp; + Dashboards | 09/06/2026 | 09/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - Tìm hiểu AWS CloudTrail <br>&emsp; + Event History <br>&emsp; + Trails <br>&emsp; + Audit Logging <br> - Thực hành theo dõi các API Calls | 10/06/2026 | 10/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - Tìm hiểu AWS X-Ray <br>&emsp; + Distributed Tracing <br>&emsp; + Trace Map <br>&emsp; + Service Map <br>&emsp; + Phân tích hiệu năng | 12/06/2026 | 11/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Thực hành:** <br>&emsp; + Tạo CloudWatch Alarm <br>&emsp; + Giám sát EC2 và Lambda <br>&emsp; + Kích hoạt CloudTrail <br>&emsp; + Phân tích Trace bằng AWS X-Ray | 12/06/2026 | 14/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 8:

* Hiểu được vai trò của **Monitoring**, **Logging** và **Observability** trong việc vận hành và giám sát hệ thống trên nền tảng AWS.

* Nắm được các chức năng chính của **Amazon CloudWatch**, bao gồm:
  * CloudWatch Metrics
  * CloudWatch Logs
  * CloudWatch Alarms
  * CloudWatch Dashboards

* Thực hành giám sát tài nguyên AWS bằng Amazon CloudWatch:
  * Theo dõi hiệu suất của EC2.
  * Giám sát các chỉ số hoạt động của Lambda.
  * Quan sát CPU Utilization và các Metrics quan trọng.
  * Tạo Dashboard để theo dõi trạng thái hệ thống.

* Tạo và cấu hình **CloudWatch Alarms** để tự động phát hiện các trường hợp vượt ngưỡng và gửi cảnh báo khi cần thiết.

* Tìm hiểu **AWS CloudTrail** và hiểu cách dịch vụ ghi lại toàn bộ hoạt động trên tài khoản AWS, bao gồm:
  * Event History.
  * API Calls.
  * Hoạt động của người dùng.
  * Audit Logging.

* Có khả năng theo dõi và kiểm tra lịch sử thao tác trên tài khoản AWS phục vụ cho mục đích kiểm toán và bảo mật.

* Tìm hiểu **AWS X-Ray**, bao gồm:
  * Distributed Tracing.
  * Trace Map.
  * Service Map.
  * Phân tích Request.
  * Xác định các điểm gây chậm trong ứng dụng.

* Thực hành theo dõi luồng xử lý của Request giữa nhiều dịch vụ AWS nhằm phân tích hiệu năng và xác định nguyên nhân gây độ trễ.

* Xây dựng thành công một hệ thống Monitoring cơ bản bằng cách kết hợp:
  * Amazon CloudWatch.
  * AWS CloudTrail.
  * AWS X-Ray.

* Có khả năng giám sát tài nguyên AWS, theo dõi hiệu năng ứng dụng, phân tích nhật ký hoạt động và hỗ trợ phát hiện sự cố trong quá trình vận hành hệ thống.