---
title: "Worklog Tuần 7"
date: 2024-01-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---


### Mục tiêu tuần 7:

* Tìm hiểu khái niệm Container và Container Orchestration.
* Làm quen với Docker và các thành phần cơ bản.
* Tìm hiểu Amazon Elastic Container Registry (ECR).
* Tìm hiểu Amazon Elastic Container Service (ECS).
* Thực hành đóng gói và triển khai một ứng dụng web bằng Docker trên AWS.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --------- | ------------ | --------------- | -------------- |
| 2 | - Tìm hiểu Container và Docker <br> - Tìm hiểu Image, Container, Dockerfile <br> - Tìm hiểu vòng đời của Container | 01/06/2026 | 01/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Cài đặt Docker <br> - Thực hành các lệnh Docker cơ bản <br>&emsp; + docker build <br>&emsp; + docker run <br>&emsp; + docker ps <br>&emsp; + docker images | 02/06/2026 | 02/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - Tìm hiểu Amazon ECR <br> - Tạo Repository <br> - Push Docker Image lên Amazon ECR | 03/06/2026 | 03/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - Tìm hiểu Amazon ECS <br>&emsp; + ECS Cluster <br>&emsp; + Task Definition <br>&emsp; + ECS Service <br>&emsp; + Launch Type | 04/06/2026 | 04/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Thực hành:** <br>&emsp; + Build ứng dụng Web bằng Docker <br>&emsp; + Push Image lên Amazon ECR <br>&emsp; + Deploy ứng dụng bằng Amazon ECS | 05/06/2026 | 05/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 7:

* Hiểu được khái niệm **Container** và những lợi ích của việc đóng gói ứng dụng bằng Docker.

* Phân biệt được các thành phần trong Docker, bao gồm:
  * Docker Engine
  * Docker Image
  * Docker Container
  * Dockerfile
  * Docker Hub

* Cài đặt và sử dụng Docker để:
  * Build Docker Image.
  * Chạy và quản lý Container.
  * Kiểm tra danh sách Image và Container.
  * Xóa Image và Container không sử dụng.

* Hiểu được quy trình đóng gói một ứng dụng thành Docker Image thông qua Dockerfile.

* Tìm hiểu và sử dụng **Amazon Elastic Container Registry (ECR)**:
  * Tạo Private Repository.
  * Đăng nhập vào Amazon ECR.
  * Tag Docker Image.
  * Push Image lên ECR.
  * Quản lý các phiên bản Image.

* Hiểu kiến trúc của **Amazon Elastic Container Service (ECS)**, bao gồm:
  * ECS Cluster.
  * Task Definition.
  * ECS Task.
  * ECS Service.
  * Launch Type (EC2 và Fargate).

* Hiểu được quy trình triển khai Container trên AWS:
  * Build Docker Image.
  * Push Image lên Amazon ECR.
  * Tạo ECS Cluster.
  * Tạo Task Definition.
  * Triển khai ECS Service.

* Thực hành triển khai một ứng dụng Web dạng Container trên Amazon ECS và kiểm tra trạng thái hoạt động của Service.

* Hiểu được vai trò của Amazon ECR trong việc lưu trữ Docker Image và Amazon ECS trong việc quản lý, mở rộng và triển khai Container.

* Có khả năng xây dựng quy trình triển khai ứng dụng Container từ môi trường phát triển lên AWS bằng Docker, Amazon ECR và Amazon ECS.