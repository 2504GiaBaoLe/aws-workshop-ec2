---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---


#### Giới thiệu về Mimi Jewelry E-commerce Project

- **Mimi Jewelry E-commerce Project** là một hệ thống website bán hàng được xây dựng theo mô hình serverless trên AWS. Dự án tập trung vào việc triển khai một ứng dụng thương mại điện tử có đầy đủ các chức năng cơ bản như xem sản phẩm, đăng nhập, quản lý giỏ hàng, đặt hàng, thanh toán online, quản lý đơn hàng và upload hình ảnh sản phẩm.

- Kiến trúc của dự án sử dụng các dịch vụ AWS managed services và serverless services để giảm việc quản lý server thủ công. Thay vì triển khai hệ thống trên EC2 hoặc tự quản lý máy chủ, dự án sử dụng **Amazon S3, Amazon CloudFront, Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, Amazon EventBridge, Amazon CloudWatch, Amazon SNS, AWS IAM, AWS SAM, AWS WAF và AWS CloudFormation**.

- Dự án cũng tích hợp **ZaloPay Sandbox** để mô phỏng quy trình thanh toán online. Ngoài ra, hệ thống còn có các tác vụ nền như kiểm tra đơn hàng chưa thanh toán, xử lý hoàn tiền và gửi cảnh báo qua CloudWatch Alarm kết hợp với SNS.

- Mục tiêu chính của dự án là xây dựng một hệ thống ecommerce có thể triển khai thật trên AWS, có khả năng mở rộng, dễ vận hành, có monitoring, có CI/CD và phù hợp với định hướng production-like system.

#### Nội dung

1. [Tổng quan về workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequiste/)
3. [Truy cập đến S3 từ VPC](5.3-S3-vpc/)
4. [Truy cập đến S3 từ TTDL On-premises](5.4-S3-onprem/)
5. [VPC Endpoint Policies (làm thêm)](5.5-Policy/)
6. [Dọn dẹp tài nguyên](5.6-Cleanup/)
