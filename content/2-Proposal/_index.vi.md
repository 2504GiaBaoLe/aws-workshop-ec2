---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

{{% notice info %}}
Dự án đề xuất xây dựng **Mimi Jewelry E-commerce Platform** theo kiến trúc **AWS Serverless** nhằm cung cấp một nền tảng thương mại điện tử hiện đại, có khả năng mở rộng, bảo mật và tối ưu chi phí vận hành.
{{% /notice %}}


Tại phần này, nhóm trình bày tổng quan giải pháp, mục tiêu, kiến trúc và kế hoạch triển khai của dự án Mimi Jewelry E-commerce Platform.

# Mimi Jewelry E-commerce Platform  
## Giải pháp thương mại điện tử Serverless trên AWS  

### 1. Tóm tắt điều hành

**Mimi Jewelry E-commerce Platform** là nền tảng thương mại điện tử được xây dựng trên kiến trúc **AWS Serverless** nhằm cung cấp hệ thống bán hàng trực tuyến hiện đại, bảo mật và dễ mở rộng. Hệ thống hỗ trợ quản lý sản phẩm, đặt hàng, thanh toán trực tuyến và trang quản trị dành cho quản trị viên.

Giải pháp sử dụng Amazon Route 53, AWS WAF, Amazon CloudFront, Amazon S3, Amazon Cognito, Amazon API Gateway, AWS Lambda và Amazon DynamoDB để xây dựng một hệ thống cloud-native. Việc triển khai theo mô hình serverless giúp giảm chi phí vận hành và tự động mở rộng theo lưu lượng truy cập.

Ngoài ra, hệ thống tích hợp ZaloPay để xử lý thanh toán, GitHub Actions để triển khai CI/CD và AWS CloudFormation để quản lý toàn bộ hạ tầng dưới dạng Infrastructure as Code.

### Mục tiêu dự án

- Xây dựng website thương mại điện tử theo kiến trúc AWS Serverless.
- Giảm chi phí vận hành bằng mô hình Pay-as-you-go.
- Tự động triển khai bằng GitHub Actions và CloudFormation.
- Tích hợp xác thực người dùng và thanh toán trực tuyến.
- Áp dụng các dịch vụ bảo mật và giám sát của AWS.


### 2. Tuyên bố vấn đề  
*Vấn đề hiện tại*  
Các hệ thống thương mại điện tử truyền thống thường được triển khai trên các máy chủ vật lý hoặc máy chủ ảo, yêu cầu người phát triển phải quản lý toàn bộ hạ tầng bao gồm máy chủ ứng dụng, cơ sở dữ liệu, hệ thống lưu trữ, cân bằng tải, bảo mật, giám sát và sao lưu dữ liệu. Khi số lượng người dùng tăng lên, việc mở rộng tài nguyên thường đòi hỏi cấu hình bổ sung hoặc nâng cấp hệ thống, gây tốn thời gian và chi phí.

Đối với các dự án cá nhân hoặc nhóm nhỏ, việc đầu tư và vận hành hạ tầng theo mô hình truyền thống không chỉ làm tăng chi phí mà còn yêu cầu kiến thức chuyên sâu về quản trị hệ thống. Bên cạnh đó, việc tích hợp các chức năng như xác thực người dùng, thanh toán trực tuyến, lưu trữ hình ảnh sản phẩm, giám sát hệ thống và tự động triển khai thường phải sử dụng nhiều công nghệ khác nhau, dẫn đến quá trình phát triển trở nên phức tạp.

Ngoài ra, các website thương mại điện tử cần đáp ứng nhiều yêu cầu về bảo mật, tính sẵn sàng và khả năng mở rộng. Nếu không được thiết kế phù hợp, hệ thống có thể gặp các vấn đề như hiệu năng thấp khi lưu lượng truy cập tăng, rủi ro bảo mật hoặc chi phí vận hành cao do tài nguyên luôn phải hoạt động liên tục.  

*Giải pháp*  
Để giải quyết các vấn đề trên, dự án lựa chọn kiến trúc AWS Serverless nhằm giảm thiểu việc quản lý hạ tầng và tận dụng khả năng mở rộng tự động của các dịch vụ AWS.

Người dùng truy cập website thông qua tên miền được quản lý bởi Amazon Route 53. Mọi yêu cầu trước tiên được chuyển đến Amazon CloudFront, nơi phân phối nội dung tĩnh với độ trễ thấp trên phạm vi toàn cầu. Trước CloudFront, hệ thống triển khai AWS WAF nhằm kiểm tra và lọc các yêu cầu bất thường, góp phần tăng cường bảo mật cho ứng dụng.

Phần giao diện người dùng được xây dựng dưới dạng Static Website và lưu trữ trên Amazon S3, sau đó được CloudFront phân phối đến người dùng. Việc xác thực người dùng và quản trị viên được thực hiện thông qua Amazon Cognito, giúp quản lý đăng nhập, đăng ký và phát hành JWT Token để bảo vệ các API.

Mọi yêu cầu từ giao diện được gửi đến Amazon API Gateway, đóng vai trò cổng giao tiếp giữa frontend và backend. API Gateway sẽ kích hoạt các AWS Lambda Functions để xử lý toàn bộ nghiệp vụ của hệ thống như quản lý sản phẩm, xử lý đơn hàng, tạo liên kết tải lên hình ảnh, thực hiện thanh toán, quản lý tài khoản và các chức năng quản trị.

Thông tin sản phẩm và đơn hàng được lưu trữ trong Amazon DynamoDB, cung cấp khả năng mở rộng linh hoạt và hiệu suất cao cho các thao tác đọc ghi. Hình ảnh sản phẩm được lưu trong Amazon S3 Product Image Bucket, trong đó hệ thống sử dụng Pre-signed URL để cho phép quản trị viên tải ảnh trực tiếp lên S3 mà không cần truyền dữ liệu qua máy chủ backend.

Hệ thống tích hợp ZaloPay để xử lý thanh toán trực tuyến. Khi khách hàng thực hiện thanh toán, Lambda tạo yêu cầu thanh toán và gửi tới ZaloPay. Sau khi giao dịch hoàn tất, ZaloPay gửi callback về hệ thống để cập nhật trạng thái đơn hàng trong DynamoDB.

Đối với các tác vụ chạy nền, Amazon EventBridge được sử dụng để kích hoạt các Lambda theo lịch nhằm kiểm tra các đơn hàng quá hạn thanh toán và thực hiện đối soát hoàn tiền khi cần thiết. Toàn bộ log và chỉ số hoạt động của hệ thống được ghi nhận bởi Amazon CloudWatch. Khi phát hiện lỗi hoặc vượt ngưỡng cảnh báo, CloudWatch sẽ gửi thông báo thông qua Amazon SNS đến email của quản trị viên.

Quá trình triển khai được tự động hóa bằng GitHub Actions, sử dụng IAM OIDC Role để xác thực với AWS mà không cần lưu trữ Access Key trong repository. Hạ tầng được triển khai thông qua AWS CloudFormation, giúp đảm bảo tính nhất quán và dễ dàng tái triển khai trong các môi trường khác nhau.  

*Lợi ích và hoàn vốn đầu tư (ROI)*  
Giải pháp giúp giảm đáng kể công việc quản lý máy chủ, đồng thời tận dụng khả năng tự động mở rộng của AWS để đáp ứng sự thay đổi về lưu lượng truy cập mà không cần can thiệp thủ công. Việc sử dụng các dịch vụ serverless như Amazon S3, Amazon CloudFront, Amazon API Gateway, AWS Lambda và Amazon DynamoDB giúp tối ưu chi phí nhờ mô hình pay-as-you-go, chỉ tính phí theo mức sử dụng thực tế.

Bên cạnh lợi ích về mặt kỹ thuật, dự án còn là cơ hội để áp dụng nhiều dịch vụ AWS trong một hệ thống hoàn chỉnh, bao gồm lưu trữ, mạng, xác thực, xử lý dữ liệu, giám sát, bảo mật và tự động triển khai. Điều này giúp nâng cao kiến thức thực tế về kiến trúc cloud-native và quy trình triển khai ứng dụng trên AWS.

Trong giai đoạn học tập và trình diễn, chi phí hạ tầng dự kiến duy trì ở mức 0–10 USD mỗi tháng nếu lưu lượng truy cập thấp và thực hiện dọn dẹp tài nguyên sau khi sử dụng. Khi triển khai thực tế, hệ thống có thể mở rộng để phục vụ nhiều người dùng hơn mà không cần thay đổi kiến trúc tổng thể, từ đó giảm chi phí đầu tư ban đầu và nâng cao hiệu quả vận hành lâu dài.

### 3. Kiến trúc giải pháp  
Hệ thống được thiết kế theo kiến trúc AWS Serverless. Người dùng truy cập website thông qua Amazon Route 53. Route 53 điều hướng request đến Amazon CloudFront. Trước CloudFront, hệ thống có AWS WAF để kiểm tra request và hỗ trợ bảo vệ ứng dụng khỏi các truy cập bất thường.

CloudFront phân phối frontend từ Amazon S3 Frontend Bucket. Frontend là static website được build từ source code và lưu trong S3. Khi người dùng truy cập website, CloudFront phục vụ các file static như HTML, CSS và JavaScript.

Hệ thống sử dụng Amazon Cognito để xác thực người dùng. Cognito quản lý quá trình đăng nhập và trả về token xác thực. Token này được frontend gửi kèm trong các request đến Amazon API Gateway. API Gateway đóng vai trò là API Layer, tiếp nhận request và invoke AWS Lambda để xử lý nghiệp vụ.

Compute Layer của hệ thống sử dụng AWS Lambda. Các Lambda chính gồm EcommerceBackendFunction, RefundReconciliationFunction và PaymentExpirySchedulerFunction. EcommerceBackendFunction xử lý các nghiệp vụ chính như quản lý sản phẩm, đơn hàng, checkout, upload hình ảnh, thanh toán và admin operations. RefundReconciliationFunction dùng để đối soát hoàn tiền. PaymentExpirySchedulerFunction dùng để kiểm tra các đơn hàng chờ thanh toán nhưng đã quá hạn.

Data Layer sử dụng Amazon DynamoDB để lưu dữ liệu. Các bảng chính gồm Products và Orders. Products lưu thông tin sản phẩm, giá, mô tả, hình ảnh, tồn kho và trạng thái sản phẩm. Orders lưu thông tin đơn hàng, khách hàng, trạng thái thanh toán và trạng thái xử lý đơn hàng.

S3 Product Image Bucket được dùng để lưu hình ảnh sản phẩm. Khi admin upload ảnh, backend xử lý upload image và lưu ảnh vào bucket này. Sau đó ảnh được dùng để hiển thị trên website.

Integration Layer gồm Amazon EventBridge và ZaloPay. EventBridge kích hoạt các Lambda chạy nền theo lịch. ZaloPay là Third Party Service dùng để xử lý thanh toán trực tuyến. Lambda gửi payment request đến ZaloPay, sau đó ZaloPay trả kết quả thanh toán về hệ thống.

Monitoring Layer gồm Amazon CloudWatch, Amazon SNS, Amazon Cognito AdminPool và Alert Email. CloudWatch ghi logs và metrics từ API Gateway, Lambda và các service liên quan. Khi phát sinh lỗi hoặc vượt ngưỡng cảnh báo, CloudWatch gửi alarm đến SNS. SNS tiếp tục gửi thông báo đến email admin. AdminPool Cognito được dùng để xác thực admin trong hệ thống quản trị và hỗ trợ kiểm soát quyền truy cập của admin.

CI/CD Layer sử dụng GitHub, GitHub Actions, IAM OIDC Role và CloudFormation. Developer push code lên GitHub. GitHub Actions build và deploy hệ thống lên AWS thông qua IAM OIDC Role. CloudFormation triển khai các tài nguyên như Lambda Function, API Gateway, S3 Frontend, CloudFront Invalidation và các tài nguyên liên quan.

<img src="/images/2-Proposal/aws-ecommerce-architecture.jpg"
     alt="AWS Serverless E-commerce Architecture"
     style="max-width:100%;height:auto;">

Dịch vụ AWS sử dụng

- Amazon Route 53: Quản lý domain và DNS cho website.
- AWS WAF: Kiểm tra request và tăng cường bảo vệ ứng dụng trước CloudFront.
- Amazon CloudFront: Phân phối frontend và nội dung tĩnh thông qua CDN.
- Amazon S3 Frontend Bucket: Lưu trữ static files của frontend.
- Amazon S3 Product Image Bucket: Lưu trữ hình ảnh sản phẩm.
- Amazon Cognito: Quản lý đăng nhập và xác thực người dùng/admin.
- Amazon API Gateway: Tiếp nhận request từ frontend và chuyển đến Lambda.
- AWS Lambda: Xử lý backend logic, đơn hàng, sản phẩm, thanh toán, upload ảnh và tác vụ nền.
- Amazon DynamoDB: Lưu dữ liệu sản phẩm và đơn hàng.
- Amazon EventBridge: Kích hoạt Lambda chạy nền theo lịch.
- Amazon CloudWatch: Ghi logs, metrics và hỗ trợ tạo alarm.
- Amazon SNS: Gửi cảnh báo vận hành qua email.
- AWS IAM: Quản lý quyền truy cập giữa các dịch vụ.
- AWS CloudFormation: Triển khai và quản lý hạ tầng bằng template.

Dịch vụ bên thứ ba

- ZaloPay: Cổng thanh toán bên thứ ba để xử lý giao dịch thanh toán.
- GitHub Actions: Tự động hóa quy trình build và deploy.

Thiết kế thành phần

- User Layer: Người dùng truy cập website thông qua domain.
- DNS Layer: Route 53 điều hướng request đến CloudFront.
- Security Layer: AWS WAF kiểm tra request trước khi vào CloudFront.
- Frontend Layer: CloudFront phân phối frontend từ S3 Frontend Bucket.
- Authentication Layer: Cognito quản lý đăng nhập và token xác thực.
- API Layer: API Gateway tiếp nhận request và gọi Lambda.
- Compute Layer: AWS Lambda xử lý toàn bộ nghiệp vụ backend.
- Data Layer: DynamoDB lưu Products và Orders.
- Storage Layer: S3 Product Image Bucket lưu hình ảnh sản phẩm.
- Integration Layer: EventBridge xử lý tác vụ nền, ZaloPay xử lý thanh toán.
- Monitoring Layer: CloudWatch, SNS và Alert Email theo dõi lỗi hệ thống.
- CI/CD Layer: GitHub Actions, IAM OIDC Role và CloudFormation triển khai hệ thống.

### 4. Triển khai kỹ thuật  
*Các giai đoạn triển khai*    
1. Nghiên cứu dịch vụ AWS: Tìm hiểu S3, CloudFront, Route 53, WAF, Cognito, API Gateway, Lambda, DynamoDB, EventBridge, CloudWatch, SNS, IAM và CloudFormation.  
2. Thiết kế kiến trúc: Xây dựng sơ đồ kiến trúc serverless cho hệ thống ecommerce.  
3. Phát triển frontend: Xây dựng giao diện website bán hàng, trang sản phẩm, checkout và trang admin.  
4. Phát triển backend: Xây dựng backend bằng Node.js/TypeScript chạy trên AWS Lambda.
5. Cấu hình API Layer: Tạo API Gateway để tiếp nhận request từ frontend.
6. Tích hợp database: Sử dụng DynamoDB để lưu Products và Orders.
7. Tích hợp authentication: Sử dụng Cognito cho đăng nhập user/admin.
8. Triển khai frontend: Upload static frontend lên S3 và phân phối qua CloudFront.
9. Bổ sung bảo mật: Cấu hình AWS WAF để kiểm tra request vào CloudFront.
10. Upload hình ảnh: Sử dụng S3 Product Image Bucket để lưu ảnh sản phẩm.
11. Tích hợp thanh toán: Tích hợp ZaloPay để xử lý payment request và callback.
12. Tác vụ nền: Dùng EventBridge để trigger Lambda theo lịch.
13. Monitoring và alert: Cấu hình CloudWatch, SNS và Alert Email.
14. CI/CD: Sử dụng GitHub Actions, IAM OIDC Role và CloudFormation để deploy tự động.


*Yêu cầu kỹ thuật*  
- *Frontend*: Static website, lưu trữ trên S3 và phân phối qua CloudFront.
- *Backend*: Node.js/TypeScript, AWS Lambda, API Gateway.
- *Database*: DynamoDB với bảng Products và Orders.
- *Authentication*: Amazon Cognito cho user/admin.
- *Security*: AWS WAF, IAM Policy, private S3 bucket và CloudFront.
- *Storage*: S3 Frontend Bucket và S3 Product Image Bucket.
- *Payment*: ZaloPay third-party payment service.
- *Monitoring*: CloudWatch Logs, Metrics, Alarm và SNS.
- *Deployment*: GitHub Actions, IAM OIDC Role và CloudFormation.  

### 5. Lộ trình & Mốc triển khai  
- Tuần 1–3: Học các dịch vụ AWS nền tảng và serverless.
  - Tìm hiểu AWS Console, IAM, S3, CloudFront, Route 53, WAF, DynamoDB, Cognito, Lambda, API Gateway, EventBridge, CloudWatch và SNS.

- Tuần 4: Phân tích yêu cầu và thiết kế kiến trúc.
  - Xác định chức năng hệ thống.
  - Vẽ sơ đồ kiến trúc serverless.
  - Xác định các dịch vụ AWS sử dụng.

- Tuần 5: Xây dựng frontend.
  - Trang chủ, danh sách sản phẩm, chi tiết sản phẩm.
  - Giỏ hàng, checkout và trang admin.

- Tuần 6: Xây dựng backend.
  - Tạo Lambda backend.
  - Cấu hình API Gateway.
  - Xây dựng API sản phẩm, đơn hàng và admin.

- Tuần 7: Tích hợp DynamoDB.
  - Thiết kế Products và Orders tables.
  - Kết nối Lambda với DynamoDB.

- Tuần 8: Tích hợp Cognito.
  - Cấu hình đăng nhập user/admin.
  - Xử lý JWT token và phân quyền API.

- Tuần 9: Deploy frontend.
  - Build frontend.
  - Upload lên S3.
  - Cấu hình CloudFront.

- Tuần 10: Upload ảnh và thanh toán.
  - S3 Product Image Bucket.
  - Upload image.
  - ZaloPay payment request và callback.

- Tuần 11: Scheduled jobs và monitoring.
  - EventBridge.
  - PaymentExpirySchedulerFunction.
  - RefundReconciliationFunction.
  - CloudWatch và SNS.

- Tuần 12: Hoàn thiện CI/CD, bảo mật và tài liệu.
  - GitHub Actions.
  - IAM OIDC Role.
  - CloudFormation.
  - Proposal, Workshop, Worklog và Clean-up.  

### 6. Ước tính ngân sách  

Chi phí dự án phụ thuộc vào lượng request, dung lượng lưu trữ, traffic CloudFront, số lượng log CloudWatch và việc có sử dụng domain riêng hay không.

Chi phí hạ tầng dự kiến cho giai đoạn học tập/demo

- Amazon S3: Lưu frontend và hình ảnh sản phẩm.
- Amazon CloudFront: Phân phối nội dung và hình ảnh.
- AWS WAF: Phát sinh chi phí nếu bật Web ACL và rule.
- AWS Lambda: Tính phí theo số lần gọi và thời gian chạy.
- Amazon API Gateway: Tính phí theo request.
- Amazon DynamoDB: Tính phí theo request đọc/ghi hoặc dung lượng lưu trữ.
- Amazon CloudWatch: Phát sinh chi phí từ logs, metrics và alarms.
- Amazon SNS: Chi phí thấp cho email alert.
- Amazon Route 53: Có phí hosted zone và domain nếu dùng domain riêng.
- AWS CloudFormation: Không tính phí trực tiếp cho việc quản lý stack.

Tổng chi phí dự kiến

- Giai đoạn học tập/demo: khoảng 0–10 USD/tháng nếu lượng truy cập thấp và cleanup tài nguyên đúng cách.
- Domain riêng: tính phí riêng theo năm nếu đăng ký qua Route 53.
- ZaloPay: dùng môi trường test/sandbox nên không phát sinh thanh toán thật. 

### 7. Đánh giá rủi ro  
*Ma trận rủi ro*

- Cấu hình IAM sai hoặc cấp quyền quá rộng: Ảnh hưởng cao, xác suất trung bình.
- Cấu hình WAF sai làm chặn nhầm request hợp lệ: Ảnh hưởng trung bình, xác suất thấp.
- S3 bucket policy hoặc CloudFront sai: Ảnh hưởng trung bình, xác suất trung bình.
- Lỗi Cognito/JWT token khi xác thực người dùng: Ảnh hưởng trung bình, xác suất trung bình.
- Lỗi callback thanh toán từ ZaloPay: Ảnh hưởng cao, xác suất trung bình.
- CloudWatch Logs phát sinh nhiều chi phí nếu không kiểm soát: Ảnh hưởng trung bình, xác suất thấp.
- GitHub Actions deploy lỗi do OIDC hoặc IAM role: Ảnh hưởng trung bình, xác suất trung bình.
- Route 53 domain hoặc HTTPS chưa hoàn tất: Ảnh hưởng thấp đến trung bình, xác suất trung bình.

*Chiến lược giảm thiểu*

- IAM: Áp dụng nguyên tắc least privilege cho từng role.
- WAF: Bắt đầu với rule cơ bản, theo dõi log trước khi chặn mạnh.
- S3/CloudFront: Sử dụng private bucket và chỉ phân phối qua CloudFront.
- Authentication: Kiểm tra JWT token và phân biệt rõ user/admin.
- Payment: Kiểm tra callback URL, secret key và trạng thái đơn hàng.
- Cost control: Cấu hình billing alert và cleanup tài nguyên không sử dụng.
- CI/CD: Giới hạn IAM OIDC Role theo đúng GitHub repository và branch.
- Domain: Nếu Route 53 chưa hoàn tất, dùng CloudFront URL để demo.

*Kế hoạch dự phòng*

- Nếu domain chưa sẵn sàng, sử dụng CloudFront URL.
- Nếu WAF gây lỗi request, tạm thời chuyển rule sang chế độ Count hoặc disable rule.
- Nếu GitHub Actions lỗi, deploy thủ công bằng SAM CLI và AWS CLI.
- Nếu ZaloPay callback lỗi, kiểm tra CloudWatch Logs và cập nhật trạng thái đơn hàng thủ công trong DynamoDB khi cần.
- Nếu CloudFront cache chưa cập nhật, thực hiện CloudFront Invalidation. 

### 8. Kết quả kỳ vọng  
*Cải tiến kỹ thuật*: Dự án tạo ra một hệ thống ecommerce serverless có đầy đủ các thành phần chính gồm frontend, backend API, authentication, database, image upload, payment integration, scheduled jobs, monitoring, security layer và CI/CD.
*Giá trị học tập*: Thông qua dự án, người thực hiện hiểu cách kết hợp nhiều dịch vụ AWS vào một hệ thống thực tế, bao gồm Route 53, WAF, CloudFront, S3, Cognito, API Gateway, Lambda, DynamoDB, EventBridge, CloudWatch, SNS, IAM và CloudFormation. 
*Giá trị dài hạn*: Dự án có thể tiếp tục mở rộng thành hệ thống ecommerce thực tế với domain riêng, HTTPS bằng ACM, dashboard doanh thu, tích hợp đơn vị vận chuyển, thêm cổng thanh toán khác, recommendation system và môi trường staging/production riêng biệt.