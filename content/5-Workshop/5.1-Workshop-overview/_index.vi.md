---
title: "Giới thiệu"
date: 2026-05-11
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Giới thiệu về Mimi Jewelry E-commerce Project

- **Mimi Jewelry E-commerce Project** là một hệ thống website bán hàng được xây dựng theo mô hình serverless trên AWS. Dự án tập trung vào việc triển khai một ứng dụng thương mại điện tử có đầy đủ các chức năng cơ bản như xem sản phẩm, đăng nhập, quản lý giỏ hàng, đặt hàng, thanh toán online, quản lý đơn hàng và upload hình ảnh sản phẩm.

- Kiến trúc của dự án sử dụng các dịch vụ AWS managed services và serverless services để giảm việc quản lý server thủ công. Thay vì triển khai hệ thống trên EC2 hoặc tự quản lý máy chủ, dự án sử dụng **Amazon S3, Amazon CloudFront, Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, Amazon EventBridge, Amazon CloudWatch, Amazon SNS, AWS IAM, AWS SAM, AWS WAF và AWS CloudFormation**.

- Dự án cũng tích hợp **ZaloPay Sandbox** để mô phỏng quy trình thanh toán online. Ngoài ra, hệ thống còn có các tác vụ nền như kiểm tra đơn hàng chưa thanh toán, xử lý hoàn tiền và gửi cảnh báo qua CloudWatch Alarm kết hợp với SNS.

- Mục tiêu chính của dự án là xây dựng một hệ thống ecommerce có thể triển khai thật trên AWS, có khả năng mở rộng, dễ vận hành, có monitoring, có CI/CD và phù hợp với định hướng production-like system.

#### Tổng quan về workshop

Trong workshop này, bạn sẽ triển khai một hệ thống **AWS Serverless E-commerce Platform** gồm frontend, backend, database, authentication, image storage, payment, monitoring và CI/CD.

Hệ thống được chia thành các nhóm thành phần chính như sau:

- **Frontend Layer** dùng để hiển thị giao diện website bán hàng cho người dùng và admin. Frontend được build thành static files, lưu trên **Amazon S3 Frontend Bucket** và phân phối ra Internet thông qua **Amazon CloudFront**.

- **Security & Delivery Layer** sử dụng **AWS WAF** để bảo vệ request trước khi đi vào CloudFront. CloudFront giúp tăng tốc độ truy cập, cache nội dung tĩnh và phân phối website đến người dùng với độ trễ thấp.

- **Authentication Layer** sử dụng **Amazon Cognito** để quản lý đăng ký, đăng nhập và xác thực người dùng. Dự án có thể tách riêng user pool cho customer và admin để quản lý quyền truy cập tốt hơn.

- **API Layer** sử dụng **Amazon API Gateway** để tiếp nhận request từ frontend. API Gateway đóng vai trò là cổng truy cập chính giữa frontend và backend.

- **Compute Layer** sử dụng **AWS Lambda** để xử lý logic backend như quản lý sản phẩm, giỏ hàng, đơn hàng, thanh toán, upload ảnh, xử lý callback từ ZaloPay và các tác vụ liên quan đến admin.

- **Database Layer** sử dụng **Amazon DynamoDB** để lưu dữ liệu sản phẩm, đơn hàng và hoàn tiền. DynamoDB phù hợp với kiến trúc serverless vì có khả năng mở rộng tự động, độ trễ thấp và không cần quản lý database server.

- **Storage Layer** sử dụng **Amazon S3 Product Images Bucket** để lưu hình ảnh sản phẩm. Backend tạo presigned URL để admin upload ảnh lên S3 một cách an toàn.

- **Payment Layer** tích hợp **ZaloPay Sandbox** để mô phỏng quy trình thanh toán online. Khi người dùng đặt hàng, backend tạo yêu cầu thanh toán, chuyển người dùng sang ZaloPay và xử lý kết quả thanh toán thông qua callback.

- **Async & Scheduled Processing Layer** sử dụng **Amazon EventBridge** để chạy các tác vụ định kỳ như kiểm tra đơn hàng quá hạn thanh toán và đối soát hoàn tiền.

- **Monitoring & Alert Layer** sử dụng **Amazon CloudWatch** để thu thập logs, metrics và tạo alarm. Khi hệ thống có lỗi hoặc chỉ số bất thường, **Amazon SNS** sẽ gửi email cảnh báo cho người vận hành.

- **CI/CD Layer** sử dụng **GitHub Actions** kết hợp với **AWS IAM OIDC Role**, **AWS SAM** và **AWS CloudFormation** để tự động deploy backend, cập nhật frontend lên S3 và tạo CloudFront invalidation sau mỗi lần triển khai.

#### Kiến trúc tổng quan

Luồng hoạt động chính của hệ thống có thể được mô tả như sau:

```text
User / Admin
   ↓
AWS WAF
   ↓
Amazon CloudFront
   ↓
Amazon S3 Frontend Bucket
   ↓
Amazon API Gateway
   ↓
AWS Lambda
   ↓
Amazon DynamoDB / Amazon S3 Product Images Bucket
```

Luồng thanh toán và xử lý nền:

```text
User Checkout
   ↓
API Gateway
   ↓
AWS Lambda
   ↓
ZaloPay Sandbox
   ↓
Payment Callback
   ↓
API Gateway
   ↓
Lambda
   ↓
DynamoDB Orders
```

Luồng scheduled jobs:

```text
Amazon EventBridge
   ↓
PaymentExpirySchedulerFunction / RefundReconciliationFunction
   ↓
Amazon DynamoDB
   ↓
Amazon CloudWatch Logs
```

Luồng monitoring và alert:

```text
API Gateway / Lambda / EventBridge
   ↓
Amazon CloudWatch Logs & Metrics
   ↓
CloudWatch Alarm
   ↓
Amazon SNS
   ↓
Alert Email
```

#### Các chức năng chính trong workshop

Sau khi hoàn thành workshop, hệ thống sẽ có các chức năng chính sau:

- Người dùng có thể xem danh sách sản phẩm.
- Người dùng có thể xem chi tiết sản phẩm.
- Người dùng có thể đăng ký và đăng nhập bằng Amazon Cognito.
- Người dùng có thể thêm sản phẩm vào giỏ hàng.
- Người dùng có thể tạo đơn hàng.
- Người dùng có thể thanh toán qua ZaloPay Sandbox.
- Admin có thể đăng nhập vào trang quản trị.
- Admin có thể tạo, sửa, xóa và quản lý sản phẩm.
- Admin có thể upload hình ảnh sản phẩm lên Amazon S3.
- Hệ thống lưu dữ liệu sản phẩm và đơn hàng trong DynamoDB.
- Hệ thống có scheduled jobs để xử lý đơn hàng quá hạn và hoàn tiền.
- Hệ thống có CloudWatch Logs để debug Lambda và API Gateway.
- Hệ thống có CloudWatch Alarm và SNS để gửi email cảnh báo.
- Hệ thống có CI/CD bằng GitHub Actions để tự động deploy.

#### Sơ đồ kiến trúc

<img src="/images/2-Proposal/aws-ecommerce-architecture.jpg"
     alt="AWS Serverless E-commerce Architecture"
     style="max-width:100%;height:auto;">

#### Kết quả mong đợi

Sau workshop này, bạn sẽ hiểu cách xây dựng một website bán hàng serverless trên AWS theo hướng production-like. Bạn cũng sẽ nắm được cách các dịch vụ AWS phối hợp với nhau trong một hệ thống thực tế, từ frontend, backend, authentication, database, payment, monitoring cho đến CI/CD.

Dự án này không sử dụng EC2, ECS, RDS hoặc ALB trong kiến trúc chính. Thay vào đó, hệ thống ưu tiên các dịch vụ serverless và managed services để giảm chi phí vận hành, tăng khả năng mở rộng và phù hợp với một dự án ecommerce hiện đại.
