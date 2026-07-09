---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Nội dung dưới đây được viết dựa trên quá trình tự nghiên cứu và tìm hiểu Amazon CloudWatch. Đây là ghi chú học tập và góc nhìn cá nhân, không phải tài liệu chính thức của AWS.
{{% /notice %}}

# Đừng chỉ bật CloudWatch rồi để đó
## Biến Monitoring thành "lá chắn" chủ động cho hệ thống

Trong quá trình tìm hiểu về **Amazon CloudWatch**, mình nhận ra rằng nhiều người chỉ xem CloudWatch như một công cụ để theo dõi CPU hoặc gửi email khi máy chủ gặp sự cố. Tuy nhiên, sau khi đọc tài liệu và thử tìm hiểu sâu hơn, mình nhận thấy CloudWatch có thể làm được nhiều hơn thế.

Nếu biết cách kết hợp **Metrics**, **Logs**, **Dashboards** và **Alarms**, CloudWatch không chỉ giúp quan sát hệ thống mà còn trở thành một trung tâm giám sát chủ động, có khả năng phát hiện sự cố trước khi người dùng cuối nhận ra.

## Kiến trúc tổng quan

Hình dưới đây minh họa cách Amazon CloudWatch thu thập Metrics, Logs và Events từ nhiều dịch vụ AWS, sau đó kích hoạt Alarm và các hành động tự động.

<img src="/images/3-Blog/cloudwatch-overview.jpg"
     alt="Amazon CloudWatch Overview"
     style="max-width:100%;height:auto;">

---

## CloudWatch thực sự là gì?

Amazon CloudWatch là dịch vụ **Monitoring & Observability** của AWS.

Nhiệm vụ chính của CloudWatch là thu thập dữ liệu từ các dịch vụ AWS như:

- Amazon EC2
- AWS Lambda
- Amazon RDS
- Amazon ECS
- Amazon API Gateway
- Amazon DynamoDB
- ...

Sau khi thu thập dữ liệu, CloudWatch sẽ:

- Lưu trữ Metrics
- Thu thập Logs
- Hiển thị Dashboard
- Tạo Alarm khi hệ thống có dấu hiệu bất thường

Theo góc nhìn của mình, CloudWatch giúp trả lời ba câu hỏi quan trọng:

- Hệ thống đang hoạt động như thế nào?
- Có sự cố nào đang xảy ra không?
- Nếu có thì hệ thống nên phản ứng như thế nào?

---

## Đừng chỉ theo dõi CPU

Đây là điều mình thấy khá nhiều người mắc phải khi mới sử dụng CloudWatch.

Thông thường chúng ta chỉ tạo Alarm khi CPU vượt 80%.

Thực tế CPU chỉ phản ánh một phần của hệ thống.

Theo mình, một Dashboard hiệu quả nên theo dõi đồng thời nhiều Metrics như:

- CPU Utilization
- Memory Usage
- Disk Space
- Network In / Out
- Request Count
- Error Rate
- Response Time (Latency)

Khi các Metrics được đặt cạnh nhau, việc xác định nguyên nhân gốc sẽ dễ dàng hơn rất nhiều thay vì chỉ biết rằng "server đang chậm".

---

## Metrics cho biết điều gì xảy ra, Logs giải thích vì sao

Sau khi đọc tài liệu AWS, mình thấy đây là điểm khá thú vị.

Metrics chỉ cho biết hệ thống đang gặp vấn đề.

Logs mới là nơi giúp tìm ra nguyên nhân.

Ví dụ:

- Error Rate tăng.
- Dashboard hiển thị Latency tăng cao.
- CloudWatch Logs cho thấy truy vấn Database bị Timeout.

Nhờ tập trung toàn bộ Logs của EC2, Lambda và ứng dụng về một nơi, việc phân tích lỗi sẽ nhanh hơn nhiều so với việc đăng nhập SSH vào từng máy chủ.

---

## Alarm không chỉ dùng để gửi Email

Trước đây mình nghĩ Alarm chỉ dùng để gửi Email.

Sau khi tìm hiểu thêm thì CloudWatch Alarm còn có thể kích hoạt rất nhiều hành động tự động như:

- Gửi Email bằng Amazon SNS.
- Tự động Scale EC2 thông qua Auto Scaling.
- Kích hoạt AWS Lambda.
- Chạy Systems Manager Automation.

Điều này giúp hệ thống có thể phản ứng gần như ngay lập tức khi có sự cố.

---

## Góc nhìn thực tế

Giả sử một website thương mại điện tử chuẩn bị diễn ra Flash Sale.

Lượng truy cập tăng gấp nhiều lần.

CloudWatch phát hiện:

- CPU Utilization tăng cao.
- Request Latency tăng.
- Error Rate bắt đầu xuất hiện.

Ngay lập tức:

- CloudWatch Alarm được kích hoạt.
- Auto Scaling tạo thêm EC2.
- Dashboard cập nhật theo thời gian thực.
- Amazon SNS gửi cảnh báo đến đội vận hành.

Theo mình, đây chính là giá trị lớn nhất của Monitoring.

Nếu không có CloudWatch thì rất có thể khách hàng sẽ là người đầu tiên phát hiện website gặp lỗi.

---

## Điều mình rút ra sau khi tìm hiểu

Sau khi nghiên cứu Amazon CloudWatch, mình nhận thấy đây không chỉ là công cụ theo dõi tài nguyên.

Khi kết hợp:

- Metrics
- Logs
- Dashboards
- Alarms

CloudWatch trở thành một hệ thống giám sát chủ động, giúp phát hiện sớm sự cố, tự động phản ứng và giảm đáng kể thời gian xử lý.

Theo quan điểm của mình, một hệ thống Monitoring tốt không phải là biết hệ thống đã lỗi, mà là phát hiện và xử lý vấn đề trước khi người dùng nhận ra.