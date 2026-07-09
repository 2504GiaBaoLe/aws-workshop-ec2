---
title: "Triển khai Backend"
date: 2026-05-11
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Mục tiêu

Trong phần này, chúng ta sẽ triển khai backend của dự án **Mimi Jewelry E-commerce Platform** bằng **AWS SAM** và **AWS CloudFormation**.

Backend là phần quan trọng nhất của hệ thống vì nó tạo ra các tài nguyên chính như:

- Amazon API Gateway để tiếp nhận request từ frontend.
- AWS Lambda để xử lý logic nghiệp vụ.
- Amazon DynamoDB để lưu sản phẩm, đơn hàng và hoàn tiền.
- Amazon Cognito để xác thực customer và admin.
- Amazon S3 để lưu frontend và hình ảnh sản phẩm.
- Amazon CloudFront để phân phối website.
- Amazon EventBridge để chạy các tác vụ định kỳ.
- Amazon CloudWatch để lưu logs và metrics.
- Amazon SNS để gửi email cảnh báo.
- IAM Roles và Policies cho các dịch vụ AWS.

#### Kiến trúc backend

Backend của dự án được thiết kế theo hướng serverless. Frontend gửi request đến API Gateway, sau đó API Gateway gọi Lambda để xử lý logic. Lambda sẽ đọc/ghi dữ liệu vào DynamoDB, tạo presigned URL để upload ảnh lên S3, xử lý callback thanh toán từ ZaloPay và ghi log vào CloudWatch.

Luồng xử lý backend tổng quát:

```text
Frontend
   ↓
Amazon API Gateway
   ↓
AWS Lambda
   ↓
Amazon DynamoDB
```

Luồng upload ảnh sản phẩm:

```text
Admin Frontend
   ↓
API Gateway
   ↓
Lambda tạo presigned URL
   ↓
Amazon S3 Product Images Bucket
```

Luồng xử lý tác vụ nền:

```text
Amazon EventBridge
   ↓
Scheduled Lambda Function
   ↓
Amazon DynamoDB
   ↓
Amazon CloudWatch Logs
```

#### Di chuyển vào thư mục backend

Mở terminal tại thư mục project:

```text
C:\Users\namda\ecommerce-aws
```

Sau đó di chuyển vào thư mục backend:

```bash
cd backend
```

Kiểm tra các file chính trong thư mục backend:

```text
backend/
├── src/
├── template.yaml
├── package.json
├── tsconfig.json
└── samconfig.toml
```

Trong đó:

- `src/` chứa source code Lambda.
- `template.yaml` là file khai báo hạ tầng AWS bằng AWS SAM.
- `package.json` chứa script build và dependencies.
- `tsconfig.json` dùng để cấu hình TypeScript.
- `samconfig.toml` lưu cấu hình deploy sau khi chạy `sam deploy --guided`.

#### Kiểm tra AWS CLI

Trước khi deploy, kiểm tra AWS CLI đã đăng nhập đúng tài khoản chưa:

```bash
aws sts get-caller-identity
```

Kết quả mong đợi:

```json
{
  "UserId": "AIDAxxxxxxxxxxxxx",
  "Account": "451626662985",
  "Arn": "arn:aws:iam::451626662985:user/your-user"
}
```

Kiểm tra region hiện tại:

```bash
aws configure get region
```

Region sử dụng trong workshop:

```text
ap-southeast-1
```

<img src="/images/5-Workshop/5.3-Backend-deployment/account.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

Nếu region chưa đúng, cấu hình lại:

```bash
aws configure
```

#### Cài đặt dependencies cho backend

Trong thư mục `backend`, chạy lệnh:

```bash
npm install
```

Lệnh này sẽ cài các thư viện cần thiết để build và deploy backend.

Sau khi cài xong, kiểm tra file `node_modules` đã được tạo:

```text
backend/node_modules/
```

#### Kiểm tra template.yaml

File `template.yaml` là file quan trọng nhất của backend. File này định nghĩa toàn bộ hạ tầng serverless của dự án.

Các tài nguyên chính trong `template.yaml` gồm:

- `EcommerceBackendFunction`: Lambda chính xử lý API.
- `RefundReconciliationFunction`: Lambda xử lý đối soát hoàn tiền.
- `PaymentExpirySchedulerFunction`: Lambda xử lý đơn hàng quá hạn thanh toán.
- `HttpApi`: API Gateway HTTP API.
- `ProductsTable`: DynamoDB table lưu sản phẩm.
- `OrdersTable`: DynamoDB table lưu đơn hàng.
- `CustomerUserPool`: Cognito User Pool cho customer.
- `AdminUserPool`: Cognito User Pool cho admin.
- `FrontendBucket`: S3 bucket chứa frontend static files.
- `ProductImagesBucket`: S3 bucket chứa hình ảnh sản phẩm.
- `CloudFrontDistribution`: CloudFront distribution cho website.
- `SNSTopic`: SNS topic gửi cảnh báo.
- `CloudWatchAlarms`: các cảnh báo vận hành hệ thống.

#### Build backend bằng AWS SAM

Chạy lệnh sau trong thư mục `infrastructure`:

```bash
sam build
```

AWS SAM sẽ đọc file `template.yaml`, build Lambda source code và chuẩn bị artifact để deploy lên AWS.

Kết quả mong đợi:

```text
Build Succeeded
```

Nếu build thành công, SAM sẽ tạo thư mục:

```text
infrastructure/.aws-sam/
```

Thư mục này chứa artifact đã build và được sử dụng trong bước deploy.

#### Deploy backend lần đầu bằng SAM

Chạy lệnh:

```bash
sam deploy --guided
```

Khi SAM hỏi thông tin, nhập như sau:

```text
Stack Name: ecommerce-aws-backend
AWS Region: ap-southeast-1
Confirm changes before deploy: y
Allow SAM CLI IAM role creation: y
Disable rollback: n
Save arguments to configuration file: y
SAM configuration file: samconfig.toml
SAM configuration environment: default
```

Giải thích:

- `Stack Name` là tên CloudFormation stack.
- `AWS Region` là region triển khai hệ thống.
- `Confirm changes before deploy` giúp xem thay đổi trước khi deploy.
- `Allow SAM CLI IAM role creation` cho phép SAM tạo IAM Role cho Lambda.
- `Save arguments to configuration file` giúp lưu cấu hình vào `samconfig.toml` để các lần sau deploy nhanh hơn.

#### Xác nhận deploy

Sau khi chạy `sam deploy --guided`, SAM sẽ hiển thị danh sách tài nguyên sẽ được tạo hoặc cập nhật.

Nếu không có vấn đề, nhập:

```text
y
```

để xác nhận deploy.

Quá trình deploy có thể mất vài phút vì CloudFormation cần tạo nhiều tài nguyên như Lambda, API Gateway, DynamoDB, Cognito, S3, CloudFront và IAM Roles.

#### Deploy lại sau khi đã có samconfig.toml

Sau lần deploy đầu tiên, các lần sau chỉ cần chạy:

```bash
sam build
sam deploy
```

Không cần nhập lại toàn bộ cấu hình vì SAM đã lưu trong file:

```text
backend/samconfig.toml
```

#### Kiểm tra CloudFormation stack

Sau khi deploy xong, vào AWS Console:

```text
CloudFormation → Stacks
```

Chọn stack:

```text
ecommerce-aws-backend
```

Trạng thái mong đợi:

```text
CREATE_COMPLETE
```

Nếu là lần cập nhật sau, trạng thái có thể là:

```text
UPDATE_COMPLETE
```

<img src="/images/5-Workshop/5.3-Backend-deployment/done.png" alt="CloudFormation Stack Complete" style="max-width: 100%; height: auto;">


#### Kiểm tra Outputs của stack

Trong CloudFormation stack, mở tab:

```text
Outputs
```

Tại đây, bạn sẽ thấy các thông tin quan trọng như:

- API Gateway URL.
- CloudFront URL.
- Frontend Bucket Name.
- Product Images Bucket Name.
- Customer User Pool ID.
- Customer User Pool Client ID.
- Admin User Pool ID.
- Admin User Pool Client ID.

Ví dụ:

```text
ApiUrl:
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod

CloudFrontUrl:
https://dfjng6e5jz4mf.cloudfront.net
```

Các giá trị này sẽ được dùng ở phần cấu hình frontend.

<img src="/images/5-Workshop/5.3-Backend-deployment/output.png" alt="CloudFormation Outputs" style="max-width: 100%; height: auto;">

#### Kiểm tra API Gateway

Vào AWS Console:

```text
API Gateway → APIs
```

Kiểm tra API đã được tạo.

API này dùng để frontend gọi các endpoint như:

```text
/products
/product
/cart
/checkout
/account/register
/account/login
/account/orders
/admin/products
/admin/orders
/admin/refunds
/payments/zalopay/callback
```

API Gateway đóng vai trò là cổng kết nối giữa frontend và Lambda backend.

<img src="/images/5-Workshop/5.3-Backend-deployment/api-gateway.png/" alt="API Gateway" style="max-width: 100%; height: auto;">

#### Kiểm tra Lambda functions

Vào AWS Console:

```text
Lambda → Functions
```

Sau khi deploy, sẽ có các Lambda function chính:

- Lambda backend chính để xử lý request từ API Gateway.
- Lambda xử lý đơn hàng quá hạn thanh toán.
- Lambda xử lý đối soát hoàn tiền.

Các Lambda function này được tạo tự động từ `template.yaml`.

<img src="/images/5-Workshop/5.3-Backend-deployment/lambda-function.png" alt="Lambda Functions" style="max-width: 100%; height: auto;">

#### Kiểm tra DynamoDB tables

Vào AWS Console:

```text
DynamoDB → Tables
```

Các bảng chính của dự án gồm:

- `EcommerceProducts`: lưu thông tin sản phẩm.
- `EcommerceOrders`: lưu đơn hàng.
- Bảng hoặc index liên quan đến refund nếu được khai báo trong template.

Trong bảng Orders, hệ thống sử dụng Global Secondary Index để tối ưu truy vấn đơn hàng theo customer và trạng thái refund.

Ví dụ index:

```text
OrdersByCustomerIndex
RefundsByStatusIndex
```

<img src="/images/5-Workshop/5.3-Backend-deployment/dynamodb-tables.png" alt="DynamoDB Tables" style="max-width: 100%; height: auto;">

#### Kiểm tra Cognito User Pools

Vào AWS Console:

```text
Amazon Cognito → User pools
```

Dự án sử dụng Cognito để xác thực:

- Customer đăng ký, đăng nhập và xem đơn hàng cá nhân.
- Admin đăng nhập và truy cập trang quản trị.

Thông thường hệ thống sẽ có:

- Customer User Pool.
- Admin User Pool.

<img src="/images/5-Workshop/5.3-Backend-deployment/cognito.png alt="Cognito User Pools" style="max-width: 100%; height: auto;">

#### Kiểm tra S3 buckets

Vào AWS Console:

```text
Amazon S3 → Buckets
```

Sau khi deploy, hệ thống sẽ có các bucket chính:

- Frontend bucket: dùng để lưu static files của website.
- Product images bucket: dùng để lưu hình ảnh sản phẩm.

Frontend bucket sẽ được CloudFront phân phối ra Internet. Product images bucket dùng để lưu ảnh sản phẩm được upload từ trang admin.

<img src="/images/5-Workshop/5.3-Backend-deployment/s3-buckets.png" alt="S3 Buckets" style="max-width: 100%; height: auto;">

#### Kiểm tra CloudFront Distribution

Vào AWS Console:

```text
CloudFront → Distributions
```

Kiểm tra distribution đã được tạo và trạng thái là:

```text
Enabled
```

CloudFront được dùng để:

- Phân phối frontend website.
- Cache nội dung tĩnh.
- Tăng tốc truy cập.
- Kết hợp với S3 frontend bucket.
- Hỗ trợ rewrite URL cho static export của Next.js.

<img src="/images/5-Workshop/5.3-Backend-deployment/cloudfront.png" alt="CloudFront Distribution" style="max-width: 100%; height: auto;">

#### Kiểm tra EventBridge scheduled rules

Vào AWS Console:

```text
Amazon EventBridge → Scheduler
```

hoặc:

```text
Amazon EventBridge → Rules
```

Tùy cách khai báo trong template, hệ thống sẽ có các lịch chạy định kỳ như:

- Kiểm tra đơn hàng ZaloPay quá hạn thanh toán.
- Đối soát trạng thái hoàn tiền.

Các job này giúp hệ thống tự động xử lý tác vụ nền mà không cần server chạy liên tục.

<img src="/images/5-Workshop/5.3-Backend-deployment/eventbridge.png" alt="EventBridge Schedules" style="max-width: 100%; height: auto;">

#### Kiểm tra CloudWatch Logs

Vào AWS Console:

```text
CloudWatch → Logs → Log groups
```

Mỗi Lambda function sẽ có một log group riêng.

Ví dụ:

```text
/aws/lambda/ecommerce-aws-backend-...
```

CloudWatch Logs giúp kiểm tra:

- Request được gửi vào Lambda.
- Lỗi xử lý API.
- Lỗi thanh toán.
- Lỗi upload ảnh.
- Lỗi scheduled jobs.
- Thời gian xử lý và thông tin debug.

<img src="/images/5-Workshop/5.3-Backend-deployment/cloudwatch.png" alt="CloudWatch Log Groups" style="max-width: 100%; height: auto;">

#### Kiểm tra SNS Topic

Vào AWS Console:

```text
Amazon SNS → Topics
```

SNS Topic được dùng để gửi cảnh báo email khi CloudWatch Alarm phát hiện lỗi hoặc metric bất thường.

Sau khi deploy, cần kiểm tra email subscription đã được xác nhận chưa.

Trạng thái mong đợi:

```text
Confirmed
```

Nếu trạng thái vẫn là:

```text
Pending confirmation
```

hãy mở email và bấm **Confirm subscription**.

<img src="/images/5-Workshop/5.3-Backend-deployment/sns.png" alt="SNS Topic" style="max-width: 100%; height: auto;">

#### Test API public endpoint

Sau khi backend deploy thành công, có thể test API public bằng trình duyệt hoặc Postman.

Ví dụ endpoint danh sách sản phẩm:

```text
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod/products
```

Kết quả mong đợi có thể là danh sách sản phẩm hoặc mảng rỗng nếu chưa có dữ liệu:

```json
[]
```

Nếu API trả về dữ liệu hoặc response hợp lệ, nghĩa là API Gateway đã gọi Lambda thành công.

#### Test Lambda bằng CloudWatch Logs

Sau khi gọi API, mở CloudWatch Logs của Lambda backend.

Kiểm tra log mới nhất để xác nhận Lambda đã nhận request.

Log thường chứa các thông tin như:

```text
HTTP method
Request path
Status code
Error message nếu có
```

Việc kiểm tra log giúp xác định lỗi khi API chưa trả về đúng kết quả.

#### Một số lỗi thường gặp khi deploy backend

##### Lỗi thiếu quyền IAM

Nếu gặp lỗi dạng:

```text
AccessDenied
```

nghĩa là user đang deploy chưa có đủ quyền.

Cách xử lý:

- Kiểm tra IAM policy đã gắn cho user chưa.
- Kiểm tra quyền `iam:PassRole`.
- Kiểm tra quyền CloudFormation, Lambda, API Gateway, DynamoDB, S3 và Cognito.

##### Lỗi stack rollback

Nếu CloudFormation báo:

```text
ROLLBACK_IN_PROGRESS
ROLLBACK_COMPLETE
```

hãy mở tab **Events** trong CloudFormation để xem tài nguyên nào lỗi.

Sau khi tìm được nguyên nhân, sửa lại `template.yaml` hoặc tham số deploy rồi deploy lại.

##### Lỗi bucket name bị trùng

S3 bucket name phải là duy nhất toàn cầu. Nếu template dùng bucket name cố định và bị trùng, deploy có thể thất bại.

Cách xử lý:

- Đổi bucket name.
- Hoặc để CloudFormation tự sinh tên bucket.

##### Lỗi CloudFront deploy lâu

CloudFront Distribution có thể mất vài phút để chuyển sang trạng thái enabled. Đây là hành vi bình thường vì CloudFront cần phân phối cấu hình đến edge locations.

#### Kết quả đạt được

Sau khi hoàn thành phần này, bạn đã triển khai thành công backend serverless cho dự án ecommerce.

Các tài nguyên chính đã được tạo gồm:

- API Gateway HTTP API.
- Lambda backend function.
- Lambda scheduled functions.
- DynamoDB tables và indexes.
- Cognito User Pools cho customer và admin.
- S3 buckets cho frontend và product images.
- CloudFront Distribution.
- EventBridge scheduled jobs.
- CloudWatch Logs và CloudWatch Alarms.
- SNS Topic gửi cảnh báo.
- IAM Roles và Policies.

Backend hiện đã sẵn sàng để frontend kết nối và gọi API. Ở phần tiếp theo, chúng ta sẽ triển khai frontend, cấu hình biến môi trường, build Next.js app và upload website lên Amazon S3.
