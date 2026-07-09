---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Nội dung dưới đây được tổng hợp từ quá trình tìm hiểu bài viết **"Automated extraction of compressed files on Amazon S3 using AWS Batch and Amazon ECS"** trên AWS Storage Blog. Đây là ghi chú học tập và góc nhìn cá nhân, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn.
{{% /notice %}}

# Tìm hiểu kiến trúc tự động giải nén file trên Amazon S3 bằng AWS Batch và Amazon ECS

Trong quá trình tìm hiểu về **AWS Batch**, mình đã đọc bài viết **"Automated extraction of compressed files on Amazon S3 using AWS Batch and Amazon ECS"** trên AWS Storage Blog. Điều khiến mình ấn tượng nhất là cách AWS xây dựng một quy trình xử lý dữ liệu hoàn toàn tự động theo mô hình **event-driven**, trong đó tài nguyên tính toán chỉ được khởi tạo khi thực sự có dữ liệu cần xử lý.

Thay vì duy trì một máy chủ Amazon EC2 hoạt động liên tục để theo dõi và giải nén các tệp dữ liệu, AWS kết hợp nhiều dịch vụ như **Amazon S3**, **Amazon EventBridge**, **AWS Batch** và **Amazon ECS** để tạo thành một pipeline xử lý dữ liệu tự động, có khả năng mở rộng linh hoạt và tối ưu chi phí vận hành.

Trong bài viết này, mình sẽ tổng hợp lại những gì đã học được và giải thích kiến trúc theo cách mình hiểu.

---

## Bài toán đặt ra

Giả sử một doanh nghiệp thường xuyên nhận dữ liệu từ nhiều đối tác khác nhau.

Các đối tác sẽ tải lên Amazon S3 những tệp nén có dung lượng lớn (ví dụ: `.tar`). Bên trong mỗi tệp có thể chứa hàng nghìn hình ảnh, tài liệu hoặc dữ liệu cần được xử lý.

Ví dụ:

```text
backup.tar

├── image01.jpg
├── image02.jpg
├── image03.jpg
├── report.csv
└── ...
```

Tuy nhiên, các hệ thống xử lý phía sau chỉ có thể làm việc với từng tệp riêng lẻ thay vì toàn bộ tệp TAR.

Điều đó đồng nghĩa với việc cần có một quy trình trung gian để:

- Phát hiện khi có tệp mới được tải lên.
- Giải nén tệp.
- Đưa các tệp đã giải nén trở lại Amazon S3.
- Cho phép các hệ thống khác tiếp tục xử lý dữ liệu.

Nếu triển khai theo cách truyền thống, chúng ta thường phải duy trì một máy chủ Amazon EC2 chạy liên tục để theo dõi bucket và thực hiện việc giải nén. Cách tiếp cận này vừa tiêu tốn chi phí, vừa yêu cầu quản trị hạ tầng.

Đây chính là bài toán mà kiến trúc trong AWS Storage Blog hướng tới giải quyết.

---

## Kiến trúc tổng thể

Theo cách mình hiểu, toàn bộ quy trình hoạt động theo mô hình sau:

```text
Upload TAR File
        │
        ▼
   Amazon S3
        │
        ▼
 Amazon EventBridge
        │
        ▼
    AWS Batch
        │
        ▼
 Amazon ECS Container
        │
        ▼
Download → Extract → Upload
        │
        ▼
 Amazon S3 Output
```

Điểm nổi bật của kiến trúc này là **không có tài nguyên tính toán nào hoạt động thường trực**. Khi không có dữ liệu mới được tải lên, sẽ không có container hoặc máy chủ nào được tạo ra, giúp tiết kiệm đáng kể chi phí vận hành.

## Kiến trúc tổng thể

Theo cách mình hiểu, toàn bộ quy trình hoạt động như sau.

<img src="/images/3-Blog/aws-batch-architecture.jpg"
     alt="AWS Batch Architecture"
     style="max-width:100%;height:auto;">

Điểm nổi bật của kiến trúc này là không có tài nguyên tính toán nào hoạt động thường trực...


---

## Vai trò của từng dịch vụ

### Amazon S3

Amazon S3 đóng vai trò là nơi lưu trữ dữ liệu đầu vào và đầu ra.

Khi người dùng tải một tệp TAR lên bucket, Amazon S3 sẽ phát sinh sự kiện **Object Created**, từ đó kích hoạt toàn bộ quy trình xử lý.

Sau khi quá trình giải nén hoàn tất, các tệp kết quả sẽ được lưu trở lại Amazon S3 trong một bucket hoặc thư mục khác.

---

### Amazon EventBridge

Amazon EventBridge đóng vai trò là bộ điều phối sự kiện của toàn bộ hệ thống.

Dịch vụ này liên tục lắng nghe các sự kiện phát sinh từ Amazon S3. Khi phát hiện có tệp mới được tải lên, EventBridge sẽ tự động gửi yêu cầu tạo một **AWS Batch Job**.

Nhờ cơ chế này, toàn bộ pipeline được kích hoạt hoàn toàn tự động mà không cần bất kỳ thao tác thủ công nào.

---

### AWS Batch

Theo mình, AWS Batch là thành phần quan trọng nhất trong kiến trúc này.

AWS Batch chịu trách nhiệm:

- Nhận yêu cầu xử lý.
- Lựa chọn tài nguyên tính toán phù hợp.
- Khởi tạo môi trường thực thi.
- Chạy container xử lý dữ liệu.
- Giải phóng tài nguyên sau khi công việc hoàn thành.

Trước đây mình nghĩ AWS Batch chủ yếu được sử dụng cho Machine Learning hoặc High Performance Computing (HPC). Tuy nhiên, sau khi đọc bài viết này, mình nhận thấy Batch cũng rất phù hợp với các tác vụ xử lý dữ liệu theo lô (Batch Processing) có dung lượng lớn.

---

### Amazon ECS

AWS Batch không trực tiếp thực hiện việc giải nén tệp.

Thay vào đó, Batch sẽ khởi chạy một Docker Container thông qua Amazon ECS.

Container này sẽ thực hiện toàn bộ nghiệp vụ như:

- Tải tệp từ Amazon S3.
- Giải nén dữ liệu.
- Tải kết quả trở lại Amazon S3.

Việc đóng gói toàn bộ logic xử lý trong container giúp hệ thống dễ bảo trì, dễ cập nhật và có thể triển khai trên nhiều môi trường khác nhau.

---

### Amazon EBS

Một điểm mình thấy khá thú vị là AWS không thực hiện việc giải nén trực tiếp trên Amazon S3.

Thay vào đó, container sử dụng Amazon EBS làm vùng lưu trữ tạm thời.

Quy trình diễn ra như sau:

- Tải tệp TAR từ Amazon S3 xuống Amazon EBS.
- Giải nén dữ liệu trên Amazon EBS.
- Tải toàn bộ kết quả trở lại Amazon S3.

Sau khi AWS Batch Job hoàn thành, tài nguyên lưu trữ tạm thời cũng sẽ được giải phóng cùng với môi trường tính toán.

---

## Luồng hoạt động

Theo cách mình hiểu, toàn bộ hệ thống hoạt động theo các bước sau:

1. Người dùng tải tệp TAR lên Amazon S3.
2. Amazon S3 phát sinh sự kiện **Object Created**.
3. Amazon EventBridge nhận được sự kiện.
4. EventBridge gửi yêu cầu tạo AWS Batch Job.
5. AWS Batch khởi tạo môi trường tính toán và chạy container trên Amazon ECS.
6. Container tải tệp TAR từ Amazon S3.
7. Dữ liệu được giải nén trên Amazon EBS.
8. Các tệp sau khi giải nén được tải trở lại Amazon S3.
9. AWS Batch Job kết thúc và toàn bộ tài nguyên được giải phóng.

---

## Tại sao sử dụng AWS Batch thay vì AWS Lambda?

Ban đầu mình cũng đặt câu hỏi tại sao AWS không sử dụng AWS Lambda để giải quyết bài toán này.

Sau khi tìm hiểu, mình nhận thấy nguyên nhân chủ yếu nằm ở kích thước dữ liệu.

Trong thực tế, các tệp TAR có thể có dung lượng từ hàng chục đến hàng trăm GB. Với khối lượng dữ liệu lớn như vậy, AWS Lambda sẽ gặp nhiều giới hạn về:

- Thời gian thực thi.
- Dung lượng lưu trữ tạm thời.
- Bộ nhớ và tài nguyên xử lý.

Trong khi đó, AWS Batch có thể:

- Tự động lựa chọn cấu hình Amazon EC2 phù hợp.
- Gắn thêm dung lượng lưu trữ khi cần.
- Xử lý các workload lớn trong thời gian dài.
- Tự động giải phóng tài nguyên sau khi hoàn thành.

Đây cũng là lý do AWS Batch phù hợp hơn với các tác vụ xử lý dữ liệu theo lô có dung lượng lớn.

---

## Điều mình học được

Sau khi tìm hiểu bài viết này, mình rút ra một số điểm đáng chú ý.

Thứ nhất, không phải mọi bài toán xử lý dữ liệu đều phù hợp với AWS Lambda. Đối với các workload có dung lượng lớn hoặc thời gian xử lý dài, AWS Batch là lựa chọn hiệu quả hơn.

Thứ hai, kiến trúc **event-driven** giúp hệ thống chỉ sử dụng tài nguyên khi thực sự có dữ liệu cần xử lý, từ đó tối ưu đáng kể chi phí vận hành theo mô hình **Pay-as-you-go**.

Thứ ba, việc đóng gói toàn bộ nghiệp vụ trong Docker Container giúp việc cập nhật hoặc mở rộng chức năng trở nên đơn giản mà không ảnh hưởng đến các thành phần khác trong hệ thống.

Theo mình, kiến trúc này không chỉ phù hợp với bài toán giải nén tệp dữ liệu mà còn có thể áp dụng cho nhiều kịch bản khác như:

- Resize hình ảnh hàng loạt.
- Chuyển đổi định dạng video.
- Chuyển đổi dữ liệu CSV sang Parquet.
- Phân tích log.
- OCR tài liệu.
- AI Batch Inference.
- ETL Pipeline.

Điểm chung của các bài toán này là chỉ cần xử lý khi có dữ liệu mới và thường yêu cầu nhiều tài nguyên tính toán.

---

## Kết luận

Thông qua việc tìm hiểu bài viết trên AWS Storage Blog, mình hiểu rõ hơn cách kết hợp **Amazon S3**, **Amazon EventBridge**, **AWS Batch**, **Amazon ECS** và **Amazon EBS** để xây dựng một pipeline xử lý dữ liệu hoàn toàn tự động.

Điều mình đánh giá cao nhất ở kiến trúc này là khả năng tối ưu chi phí nhờ mô hình **Pay-as-you-go**, khả năng mở rộng linh hoạt và việc giảm đáng kể công sức quản trị hạ tầng.

Trong thời gian tới, mình muốn thử triển khai lại kiến trúc này theo từng bước để hiểu sâu hơn cách các dịch vụ AWS phối hợp với nhau trong một hệ thống thực tế cũng như tích lũy thêm kinh nghiệm khi xây dựng các giải pháp xử lý dữ liệu trên nền tảng AWS.