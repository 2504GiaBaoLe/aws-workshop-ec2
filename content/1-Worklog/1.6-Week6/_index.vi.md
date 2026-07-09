---
title: "Worklog Tuần 6"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---


### Mục tiêu tuần 6:

* Tìm hiểu nhóm dịch vụ **AWS Application Integration**.
* Hiểu mô hình giao tiếp bất đồng bộ (Asynchronous Communication) và Event-driven Architecture.
* Tìm hiểu cách hoạt động của Amazon SQS, Amazon SNS, Amazon EventBridge và AWS Step Functions.
* Thực hành xây dựng một hệ thống Event-driven đơn giản bằng cách kết hợp các dịch vụ Integration.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Tìm hiểu tổng quan về AWS Application Integration <br> - Tìm hiểu Asynchronous Communication <br> - Tìm hiểu Event-driven Architecture | 25/05/2026 | 25/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Tìm hiểu Amazon SQS <br>&emsp; + Standard Queue <br>&emsp; + FIFO Queue <br>&emsp; + Visibility Timeout <br>&emsp; + Dead Letter Queue (DLQ) | 26/05/2026 | 27/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - Tìm hiểu Amazon SNS <br> - Tìm hiểu Amazon EventBridge <br> - **Thực hành:** <br>&emsp; + Tạo SNS Topic <br>&emsp; + Tạo Subscription <br>&emsp; + Tạo EventBridge Rule | 27/05/2026 | 28/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - Tìm hiểu AWS Step Functions <br>&emsp; + State Machine <br>&emsp; + Task State <br>&emsp; + Choice State <br>&emsp; + Retry & Error Handling | 28/05/2026 | 28/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Thực hành:** <br>&emsp; + Kết nối SNS → SQS → Lambda <br>&emsp; + Trigger Event bằng EventBridge <br>&emsp; + Điều phối Workflow với Step Functions | 29/05/2026 | 30/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 6:

* Hiểu được vai trò của **AWS Application Integration** trong việc kết nối và trao đổi dữ liệu giữa các dịch vụ AWS.

* Nắm được khái niệm **Asynchronous Communication** và hiểu sự khác biệt giữa giao tiếp đồng bộ (Synchronous) và bất đồng bộ (Asynchronous).

* Hiểu nguyên lý hoạt động của **Event-driven Architecture**, trong đó các dịch vụ giao tiếp thông qua sự kiện thay vì gọi trực tiếp lẫn nhau.

* Tìm hiểu và phân biệt được các tính năng của **Amazon SQS**, bao gồm:
  * Standard Queue
  * FIFO Queue
  * Visibility Timeout
  * Dead Letter Queue (DLQ)

* Thực hành tạo và quản lý **Amazon SNS**, bao gồm:
  * Tạo SNS Topic
  * Tạo Subscription
  * Gửi thông báo đến Subscriber
  * Hiểu mô hình Publish/Subscribe

* Làm quen với **Amazon EventBridge**, bao gồm:
  * Tạo EventBridge Rule
  * Thiết lập Event Pattern
  * Trigger các dịch vụ AWS dựa trên sự kiện

* Tìm hiểu **AWS Step Functions**, bao gồm:
  * Tạo State Machine
  * Task State
  * Choice State
  * Retry và Error Handling
  * Điều phối nhiều bước xử lý trong Workflow

* Thực hành xây dựng một hệ thống Event-driven bằng cách kết hợp:
  * Amazon SNS
  * Amazon SQS
  * AWS Lambda
  * Amazon EventBridge
  * AWS Step Functions

* Hiểu rõ vai trò của từng dịch vụ trong kiến trúc Event-driven:
  * SNS dùng để phát thông báo.
  * SQS dùng để lưu trữ và phân phối thông điệp.
  * EventBridge dùng để định tuyến sự kiện.
  * Step Functions dùng để điều phối quy trình xử lý.

* Có khả năng kết hợp nhiều dịch vụ AWS để xây dựng các ứng dụng có khả năng mở rộng, giảm sự phụ thuộc giữa các thành phần và hỗ trợ xử lý bất đồng bộ.