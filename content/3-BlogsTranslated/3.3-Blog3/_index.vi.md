---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Nội dung dưới đây được tổng hợp từ quá trình tìm hiểu bài viết **"How to get started with security response automation on AWS"** trên AWS Security Blog. Đây là ghi chú học tập và góc nhìn cá nhân, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn.
{{% /notice %}}

# Tìm hiểu kiến trúc tự động hóa phản ứng sự cố bảo mật trên AWS

Trong quá trình tìm hiểu về các dịch vụ bảo mật trên AWS, mình đã đọc bài viết **"How to get started with security response automation on AWS"** trên AWS Security Blog. Điều khiến mình ấn tượng nhất là cách AWS xây dựng một quy trình **Security Response Automation** theo mô hình **event-driven**, cho phép hệ thống tự động phát hiện và xử lý các sự cố bảo mật ngay khi chúng xảy ra.

Thay vì đội ngũ vận hành phải liên tục theo dõi cảnh báo, xác minh nguyên nhân và thực hiện các bước khắc phục thủ công, AWS kết hợp nhiều dịch vụ như **AWS CloudTrail**, **Amazon EventBridge**, **AWS Lambda**, **AWS Systems Manager** và **Amazon SNS** để tạo thành một pipeline phản ứng tự động. Điều này giúp giảm đáng kể thời gian xử lý sự cố, tăng khả năng phản ứng theo thời gian thực và nâng cao mức độ bảo mật cho toàn bộ hệ thống.

Trong bài viết này, mình sẽ tổng hợp lại những gì đã học được và giải thích kiến trúc theo cách mình hiểu.

---

## Bài toán đặt ra

Trong một hệ thống AWS, rất nhiều sự kiện có thể ảnh hưởng trực tiếp đến mức độ an toàn của môi trường vận hành.

Ví dụ:

- AWS CloudTrail bị vô hiệu hóa.
- Security Group được cấu hình mở toàn bộ Internet.
- Amazon S3 Bucket được đặt ở chế độ Public.
- IAM Access Key được tạo hoặc sử dụng bất thường.
- Xuất hiện các cảnh báo từ Amazon GuardDuty hoặc AWS Config.

Nếu những sự kiện này không được xử lý kịp thời, chúng có thể tạo ra các lỗ hổng bảo mật hoặc làm tăng nguy cơ bị tấn công.

Theo cách triển khai truyền thống, đội ngũ Security sẽ nhận cảnh báo, tiến hành điều tra nguyên nhân rồi mới thực hiện các bước khắc phục. Quy trình này phụ thuộc nhiều vào con người và thường mất khá nhiều thời gian.

AWS đề xuất một hướng tiếp cận khác: **tự động hóa toàn bộ quy trình phản ứng sự cố**, giúp hệ thống có thể xử lý ngay khi sự kiện được phát hiện.

---

## Kiến trúc tổng thể

Theo cách mình hiểu, toàn bộ quy trình hoạt động theo mô hình sau:

```text
Security Event
        │
        ▼
CloudTrail / GuardDuty / AWS Config
        │
        ▼
Amazon EventBridge
        │
        ▼
AWS Lambda
        │
        ▼
AWS Systems Manager Automation
        │
        ▼
Remediation + Amazon SNS Notification
```

Điểm nổi bật của kiến trúc này là **workflow chỉ được kích hoạt khi có sự kiện bảo mật xảy ra**. Điều này vừa giúp tối ưu tài nguyên vừa đảm bảo hệ thống có thể phản ứng gần như theo thời gian thực.

Hình dưới đây minh họa kiến trúc Security Response Automation được AWS đề xuất.

<img src="/images/3-Blog/security-response-architecture.jpg"
     alt="Security Response Automation Architecture"
     style="max-width:100%;height:auto;">


--- 

## Vai trò của từng dịch vụ

### AWS CloudTrail

AWS CloudTrail ghi lại toàn bộ hoạt động API diễn ra trong tài khoản AWS.

Đây là nguồn dữ liệu quan trọng giúp phát hiện các hành động bất thường như:

- Xóa hoặc vô hiệu hóa CloudTrail.
- Thay đổi IAM Policy.
- Chỉnh sửa Security Group.
- Thay đổi cấu hình Amazon S3.

CloudTrail đóng vai trò là nguồn sự kiện đầu vào cho toàn bộ hệ thống Security Response Automation.

---

### Amazon EventBridge

Amazon EventBridge là thành phần điều phối sự kiện của hệ thống.

Dịch vụ này liên tục lắng nghe các sự kiện được phát sinh từ:

- AWS CloudTrail
- Amazon GuardDuty
- AWS Config
- AWS Security Hub

Khi phát hiện một sự kiện phù hợp với các Rule đã được cấu hình, EventBridge sẽ tự động kích hoạt workflow xử lý mà không cần bất kỳ thao tác thủ công nào.

---

### AWS Lambda

AWS Lambda chịu trách nhiệm thực hiện các hành động khắc phục tự động.

Tùy theo từng loại sự cố, Lambda có thể:

- Bật lại AWS CloudTrail.
- Thu hồi IAM Access Key.
- Cập nhật Security Group.
- Chỉnh sửa Bucket Policy.
- Gọi AWS Systems Manager Automation để xử lý các quy trình phức tạp hơn.

Theo mình, Lambda chính là "bộ não" của toàn bộ kiến trúc vì nơi đây chứa logic xử lý cho từng tình huống bảo mật.

---

### AWS Systems Manager

AWS Systems Manager được sử dụng khi quá trình khắc phục yêu cầu nhiều bước hoặc cần thực hiện trên nhiều tài nguyên khác nhau.

Ví dụ:

- Cách ly một Amazon EC2 Instance nghi ngờ bị tấn công.
- Thực hiện Automation Runbook.
- Khôi phục cấu hình chuẩn.
- Chạy các quy trình Remediation nhiều bước.

Việc sử dụng Systems Manager giúp các workflow xử lý trở nên nhất quán và dễ bảo trì hơn.

---

### Amazon SNS

Sau khi quá trình khắc phục hoàn tất, Amazon SNS sẽ gửi thông báo đến đội ngũ vận hành.

Thông báo có thể được gửi qua:

- Email.
- SMS.
- HTTP Endpoint.
- Các Subscriber khác.

Điều này giúp đội ngũ Security nắm được tình trạng hệ thống và tiếp tục kiểm tra nếu cần thiết.

---

## Luồng hoạt động

Theo cách mình hiểu, toàn bộ hệ thống hoạt động theo các bước sau:

1. Một sự kiện bảo mật phát sinh trong môi trường AWS.
2. AWS CloudTrail, Amazon GuardDuty hoặc AWS Config ghi nhận sự kiện.
3. Amazon EventBridge nhận được sự kiện.
4. EventBridge kích hoạt AWS Lambda.
5. AWS Lambda hoặc AWS Systems Manager Automation thực hiện các bước khắc phục.
6. Amazon SNS gửi thông báo đến đội ngũ Security.
7. Toàn bộ quy trình hoàn thành chỉ trong vài giây mà không cần thao tác thủ công.

---

## Tại sao nên tự động hóa Security Response?

Ban đầu mình nghĩ việc gửi cảnh báo qua email là đủ để đội ngũ vận hành xử lý sự cố.

Tuy nhiên, sau khi tìm hiểu bài viết này, mình nhận ra rằng trong nhiều trường hợp, chỉ vài phút chậm trễ cũng có thể khiến rủi ro bảo mật tăng lên đáng kể.

Nếu hệ thống có thể tự động:

- Thu hồi Access Key bị lộ.
- Đóng Security Group mở Internet.
- Khôi phục Bucket Policy.
- Cô lập EC2 nghi ngờ bị tấn công.

thì thời gian phản ứng sẽ được rút ngắn từ vài phút xuống chỉ còn vài giây.

Đó cũng là giá trị lớn nhất của mô hình **Security Response Automation**.

---

## Điều mình học được

Sau khi đọc bài blog này, mình rút ra một số điểm đáng chú ý.

Thứ nhất, việc phát hiện sự cố bảo mật thôi là chưa đủ. Điều quan trọng hơn là xây dựng khả năng phản ứng tự động để giảm thời gian xử lý và hạn chế rủi ro.

Thứ hai, kiến trúc **event-driven** giúp hệ thống chỉ hoạt động khi có sự kiện phát sinh, vừa tối ưu chi phí vừa giúp việc mở rộng trở nên đơn giản hơn.

Thứ ba, việc kết hợp **AWS CloudTrail**, **Amazon EventBridge**, **AWS Lambda** và **AWS Systems Manager** giúp xây dựng một pipeline bảo mật linh hoạt, có thể áp dụng cho rất nhiều tình huống khác nhau như:

- Khắc phục Amazon S3 Bucket Public.
- Đóng Security Group mở Internet.
- Thu hồi IAM Access Key.
- Cách ly Amazon EC2 nghi ngờ bị tấn công.
- Xử lý AWS Security Hub Findings.
- Khôi phục AWS CloudTrail khi bị vô hiệu hóa.

Theo mình, đây là một kiến trúc rất thực tế và hoàn toàn có thể mở rộng để xử lý nhiều loại sự cố bảo mật khác nhau trong môi trường AWS.

---

## Kết luận

Thông qua việc tìm hiểu bài viết trên AWS Security Blog, mình hiểu rõ hơn cách AWS kết hợp **AWS CloudTrail**, **Amazon EventBridge**, **AWS Lambda**, **AWS Systems Manager**, **Amazon SNS**, **Amazon GuardDuty** và **AWS Config** để xây dựng một hệ thống **Security Response Automation** hoàn toàn tự động.

Điều mình đánh giá cao nhất ở kiến trúc này là khả năng phát hiện và phản ứng gần như theo thời gian thực, giúp giảm sự phụ thuộc vào thao tác thủ công, nâng cao hiệu quả vận hành và tăng cường mức độ bảo mật cho toàn bộ hệ thống.

Trong thời gian tới, mình muốn thử triển khai lại kiến trúc này bằng cách kết hợp **Amazon EventBridge**, **AWS Lambda** và **AWS Systems Manager Automation** để hiểu sâu hơn cách các dịch vụ AWS phối hợp với nhau trong một hệ thống bảo mật thực tế.