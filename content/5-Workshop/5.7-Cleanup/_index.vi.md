---
title: "Dọn dẹp tài nguyên"
date: 2026-05-11
weight: 7
chapter: false
pre: " <b> 5.10. </b> "
---

#### Mục tiêu

Trong phần này, chúng ta sẽ dọn dẹp các tài nguyên AWS đã tạo trong workshop **Mimi Jewelry E-commerce Platform**.

Việc dọn dẹp tài nguyên rất quan trọng sau khi hoàn thành workshop để tránh phát sinh chi phí không cần thiết.

Các nội dung chính trong phần này gồm:

- Kiểm tra lại các tài nguyên đã tạo.
- Backup dữ liệu quan trọng nếu cần.
- Empty S3 bucket trước khi xóa stack.
- Xóa CloudFormation stack bằng AWS SAM.
- Kiểm tra các tài nguyên còn sót lại.
- Xóa GitHub Actions OIDC nếu không dùng nữa.
- Xóa hoặc disable các dịch vụ liên quan như SES, SNS, CloudWatch Alarm.
- Kiểm tra billing sau khi cleanup.

#### Các tài nguyên cần dọn dẹp

Trong workshop này, hệ thống đã tạo nhiều tài nguyên AWS, bao gồm:

- Amazon S3 Frontend Bucket.
- Amazon S3 Product Images Bucket.
- Amazon CloudFront Distribution.
- Amazon API Gateway HTTP API.
- AWS Lambda Functions.
- Amazon DynamoDB Tables.
- Amazon Cognito User Pools.
- Amazon EventBridge Schedules.
- Amazon CloudWatch Logs.
- Amazon CloudWatch Alarms.
- Amazon SNS Topic.
- Amazon SES Identity.
- IAM Roles và Policies.
- AWS CloudFormation Stack.

Thông tin stack chính:

```text
CloudFormation Stack Name:
ecommerce-aws-backend

AWS Region:
ap-southeast-1
```

#### Lưu ý trước khi cleanup

Cleanup sẽ xóa tài nguyên và dữ liệu trong hệ thống.

Trước khi thực hiện, cần kiểm tra:

- Có cần giữ lại dữ liệu sản phẩm không.
- Có cần giữ lại dữ liệu đơn hàng không.
- Có cần giữ lại hình ảnh sản phẩm không.
- Có cần giữ lại user trong Cognito không.
- Có cần giữ lại source code hoặc file cấu hình không.

Nếu cần giữ dữ liệu, hãy backup trước khi xóa stack.

#### Kiểm tra CloudFormation stack

Mở AWS Console:

```text
CloudFormation → Stacks
```

Chọn stack:

```text
ecommerce-aws-backend
```

Kiểm tra các tab:

```text
Resources
Outputs
Events
```

Tab **Resources** sẽ cho biết những tài nguyên nào được tạo bởi stack.

#### Backup dữ liệu DynamoDB nếu cần

Nếu muốn giữ lại dữ liệu sản phẩm hoặc đơn hàng, có thể export hoặc scan dữ liệu trước khi xóa.

Các bảng chính:

```text
EcommerceProducts
EcommerceOrders
```

Có thể kiểm tra dữ liệu trong AWS Console:

```text
DynamoDB → Tables → EcommerceProducts → Explore table items
DynamoDB → Tables → EcommerceOrders → Explore table items
```

Nếu không cần giữ dữ liệu, có thể bỏ qua bước backup.

#### Kiểm tra S3 buckets

Mở AWS Console:

```text
Amazon S3 → Buckets
```

Các bucket chính trong workshop:

```text
ecommerce-aws-backend-frontendbucket-9wu7aid8okd8
ecommerce-aws-backend-productimagesbucket-eqn2cddpurel
```

Frontend bucket chứa static files của website.

Product images bucket chứa hình ảnh sản phẩm đã upload.

#### Empty S3 Frontend Bucket

Trước khi xóa CloudFormation stack, cần empty S3 bucket vì CloudFormation không thể xóa bucket nếu bucket còn object.

Chạy lệnh:

```bash
aws s3 rm s3://ecommerce-aws-backend-frontendbucket-9wu7aid8okd8 --recursive
```

Lệnh này sẽ xóa toàn bộ file frontend trong bucket.

Sau khi chạy xong, kiểm tra lại bucket trong AWS Console để đảm bảo bucket đã trống.

#### Empty S3 Product Images Bucket

Tiếp tục xóa toàn bộ hình ảnh sản phẩm trong Product Images Bucket:

```bash
aws s3 rm s3://ecommerce-aws-backend-productimagesbucket-eqn2cddpurel --recursive
```

Sau khi xóa xong, kiểm tra lại bucket:

```text
Amazon S3 → Buckets → Product Images Bucket
```

Bucket cần ở trạng thái không còn object.

#### Xóa object versions nếu bucket bật versioning

Nếu S3 bucket đã bật versioning, lệnh `aws s3 rm --recursive` có thể chưa xóa hết các object versions.

Khi đó, cần mở AWS Console:

```text
Amazon S3 → Buckets → chọn bucket → Versions
```

Sau đó xóa toàn bộ object versions và delete markers.

Hoặc có thể dùng AWS Console để chọn:

```text
Empty bucket
```

và xác nhận xóa toàn bộ object versions.

#### Xóa backend stack bằng AWS SAM

Di chuyển vào thư mục backend hoặc infrastructure tùy cấu trúc project:

```bash
cd C:\Users\namda\ecommerce-aws\backend
```

Chạy lệnh xóa stack:

```bash
sam delete
```

Khi SAM hỏi xác nhận, nhập:

```text
y
```

SAM sẽ xóa CloudFormation stack và các tài nguyên liên quan.

Stack cần xóa:

```text
ecommerce-aws-backend
```

#### Xóa stack bằng AWS CLI

Nếu không dùng `sam delete`, có thể xóa stack bằng AWS CLI:

```bash
aws cloudformation delete-stack --stack-name ecommerce-aws-backend --region ap-southeast-1
```

Sau đó theo dõi trạng thái xóa stack:

```bash
aws cloudformation describe-stacks --stack-name ecommerce-aws-backend --region ap-southeast-1
```

Trong AWS Console, trạng thái stack sẽ chuyển sang:

```text
DELETE_IN_PROGRESS
```

Sau khi xóa thành công, stack sẽ biến mất khỏi danh sách active stacks.

#### Kiểm tra CloudFormation Events khi delete lỗi

Nếu stack xóa thất bại, vào:

```text
CloudFormation → Stacks → ecommerce-aws-backend → Events
```

Kiểm tra resource nào bị lỗi.

Một số nguyên nhân thường gặp:

- S3 bucket chưa được empty.
- S3 bucket còn object versions.
- CloudFront Distribution chưa disable xong.
- IAM Role đang được sử dụng.
- Lambda function còn event source hoặc permission liên quan.

Nếu lỗi do S3, quay lại empty bucket rồi xóa stack lại.

#### Kiểm tra CloudFront Distribution

CloudFront Distribution có thể mất vài phút để được xóa hoàn toàn.

Mở AWS Console:

```text
CloudFront → Distributions
```

Kiểm tra distribution:

```text
E3EAY8XN54SSDX
```

Nếu distribution vẫn còn, kiểm tra trạng thái:

```text
Enabled
Disabled
In Progress
```

Trong một số trường hợp, CloudFormation cần disable distribution trước, sau đó mới xóa được.

Quá trình này có thể mất vài phút.

#### Kiểm tra API Gateway

Sau khi stack bị xóa, kiểm tra API Gateway:

```text
API Gateway → APIs
```

API của project ecommerce không nên còn xuất hiện.

Nếu API vẫn còn, kiểm tra xem API đó có được tạo ngoài CloudFormation hay không.

API URL của workshop:

```text
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod
```

Sau khi cleanup, URL này sẽ không còn hoạt động.

#### Kiểm tra Lambda Functions

Mở AWS Console:

```text
Lambda → Functions
```

Kiểm tra các function liên quan đến project ecommerce.

Các function thường có tên chứa:

```text
ecommerce-aws-backend
```

Sau khi xóa stack, các function này cần được xóa.

Nếu vẫn còn function, kiểm tra xem function đó có được tạo ngoài stack hay không.

#### Kiểm tra DynamoDB Tables

Mở AWS Console:

```text
DynamoDB → Tables
```

Kiểm tra các bảng:

```text
EcommerceProducts
EcommerceOrders
```

Nếu CloudFormation đã xóa thành công, các bảng này sẽ không còn.

Nếu bảng vẫn còn, có thể trong `template.yaml` có cấu hình giữ lại tài nguyên hoặc bảng được tạo thủ công ngoài stack.

#### Kiểm tra Cognito User Pools

Mở AWS Console:

```text
Amazon Cognito → User pools
```

Kiểm tra Customer User Pool và Admin User Pool.

Các User Pool trong workshop:

```text
ap-southeast-1_LHSScZri7
ap-southeast-1_k57hCsM4z
```

Sau khi cleanup, các User Pool do stack tạo cần được xóa.

Nếu vẫn còn, có thể xóa thủ công trong Cognito Console nếu không còn sử dụng.

#### Kiểm tra EventBridge Schedules

Mở AWS Console:

```text
Amazon EventBridge → Scheduler
```

hoặc:

```text
Amazon EventBridge → Rules
```

Kiểm tra các scheduled jobs liên quan đến:

```text
payment expiry
refund reconciliation
ecommerce-aws-backend
```

Sau khi stack bị xóa, các schedule này cần được xóa theo.

Nếu vẫn còn schedule, xóa thủ công để tránh Lambda bị gọi ngoài ý muốn.

#### Kiểm tra CloudWatch Logs

CloudWatch Log Groups có thể không bị xóa tự động tùy cấu hình.

Mở AWS Console:

```text
CloudWatch → Logs → Log groups
```

Tìm các log group có tên chứa:

```text
/aws/lambda/ecommerce-aws-backend
```

Nếu không cần giữ logs, có thể xóa thủ công các log group này.

#### Kiểm tra CloudWatch Alarms

Mở AWS Console:

```text
CloudWatch → Alarms
```

Kiểm tra các alarm liên quan đến project ecommerce.

Sau khi stack bị xóa, các alarm do stack tạo cần được xóa.

Nếu alarm vẫn còn, có thể xóa thủ công để tránh cảnh báo không cần thiết.

#### Kiểm tra SNS Topic

Mở AWS Console:

```text
Amazon SNS → Topics
```

Kiểm tra topic dùng để gửi alert email.

Nếu topic vẫn còn và không dùng nữa, xóa topic thủ công.

Sau đó kiểm tra:

```text
Amazon SNS → Subscriptions
```

Xóa subscription email nếu không còn sử dụng.

#### Kiểm tra SES Identity

Nếu đã verify email hoặc domain trong Amazon SES, identity có thể vẫn còn sau khi xóa stack.

Mở AWS Console:

```text
Amazon SES → Configuration → Identities
```

Kiểm tra email hoặc domain đã verify.

Nếu không còn dùng SES cho project này, có thể xóa identity.

Lưu ý:

- Xóa SES identity sẽ làm email đó không còn dùng được để gửi mail qua SES.
- Nếu dùng email này cho project khác, không nên xóa.

#### Kiểm tra Secrets Manager

Nếu ZaloPay secret được tạo trong AWS Secrets Manager, cần kiểm tra xem secret có được tạo bởi stack hay tạo thủ công.

Mở AWS Console:

```text
AWS Secrets Manager → Secrets
```

Tìm secret liên quan đến:

```text
zalopay
ecommerce
```

Nếu secret không còn dùng nữa, có thể xóa.

Khi xóa secret, AWS có thể yêu cầu thời gian chờ trước khi xóa vĩnh viễn.

#### Kiểm tra IAM Roles và Policies

Mở AWS Console:

```text
IAM → Roles
```

Tìm các role liên quan đến:

```text
ecommerce-aws-backend
```

Các IAM Role do CloudFormation tạo thường sẽ bị xóa cùng stack.

Nếu vẫn còn role hoặc policy, kiểm tra kỹ trước khi xóa thủ công.

Không nên xóa role nếu role đó đang được dùng bởi project khác.

#### Kiểm tra GitHub Actions OIDC

Nếu đã tạo OIDC Provider hoặc IAM Role cho GitHub Actions CI/CD, kiểm tra trong IAM.

Mở AWS Console:

```text
IAM → Identity providers
```

Nếu có provider:

```text
token.actions.githubusercontent.com
```

chỉ xóa nếu không còn dùng cho repository hoặc project nào khác.

Kiểm tra IAM Role cho GitHub Actions:

```text
IAM → Roles
```

Tìm role liên quan đến:

```text
GitHubActions
ecommerce
OIDC
```

Nếu role chỉ dùng cho project này và không còn cần nữa, có thể xóa.

#### Kiểm tra GitHub Secrets

Trong GitHub repository, vào:

```text
Settings → Secrets and variables → Actions
```

Kiểm tra các secrets:

```text
AWS_REGION
AWS_ROLE_TO_ASSUME
ZALOPAY_APP_ID
ZALOPAY_KEY1
ZALOPAY_KEY2
```

Nếu repository không còn deploy lên AWS, có thể xóa các secrets này.

#### Kiểm tra website sau cleanup

Sau khi cleanup hoàn tất, mở CloudFront URL:

```text
https://dfjng6e5jz4mf.cloudfront.net
```

Nếu tài nguyên đã được xóa, website sẽ không còn truy cập được hoặc trả về lỗi.

Kiểm tra API URL:

```text
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod/products
```

Nếu API đã bị xóa, endpoint sẽ không còn hoạt động.

#### Kiểm tra Billing

Sau khi xóa tài nguyên, mở AWS Billing:

```text
AWS Billing and Cost Management → Bills
```

Kiểm tra các dịch vụ có thể còn phát sinh chi phí:

- CloudFront.
- S3.
- DynamoDB.
- Lambda.
- API Gateway.
- CloudWatch Logs.
- SNS.
- SES.
- Secrets Manager.
- WAF nếu có dùng.

Một số dịch vụ có thể vẫn hiển thị chi phí trong ngày hiện tại do billing có độ trễ.

#### Một số lỗi thường gặp khi cleanup

##### CloudFormation không xóa được S3 bucket

Lỗi thường gặp:

```text
The bucket you tried to delete is not empty
```

Cách xử lý:

- Empty bucket.
- Xóa object versions nếu bucket bật versioning.
- Chạy lại `sam delete` hoặc delete stack.

##### CloudFront xóa lâu

CloudFront cần thời gian để disable và delete distribution.

Cách xử lý:

- Đợi vài phút.
- Kiểm tra trạng thái distribution.
- Không tạo lại stack trùng tài nguyên khi distribution cũ chưa xóa xong.

##### Stack bị DELETE_FAILED

Nếu stack chuyển sang:

```text
DELETE_FAILED
```

mở tab **Events** để xem tài nguyên nào lỗi.

Sau khi sửa nguyên nhân, chọn:

```text
Delete stack
```

và thử lại.

##### Log groups vẫn còn sau khi xóa stack

CloudWatch Log Groups đôi khi vẫn còn để giữ log lịch sử.

Cách xử lý:

- Vào CloudWatch Logs.
- Chọn log group liên quan.
- Xóa thủ công nếu không cần giữ logs.

##### DynamoDB table vẫn còn

Nếu DynamoDB table không bị xóa, có thể do:

- Có `DeletionPolicy: Retain`.
- Table được tạo thủ công ngoài stack.
- Stack delete bị lỗi trước khi xóa table.

Cách xử lý:

- Kiểm tra CloudFormation Events.
- Xóa table thủ công nếu không cần giữ dữ liệu.

#### Checklist cleanup

Sau khi hoàn tất, kiểm tra checklist sau:

- S3 Frontend Bucket đã empty hoặc bị xóa.
- S3 Product Images Bucket đã empty hoặc bị xóa.
- CloudFormation stack `ecommerce-aws-backend` đã bị xóa.
- API Gateway không còn API của project.
- Lambda functions của project đã bị xóa.
- DynamoDB tables không còn hoặc đã được backup.
- Cognito User Pools không còn nếu không dùng nữa.
- EventBridge schedules không còn chạy.
- CloudWatch Alarms đã bị xóa.
- SNS Topic và Subscriptions đã bị xóa nếu không dùng nữa.
- SES Identity đã được xóa nếu không cần giữ.
- Secrets Manager secrets đã được xóa nếu không dùng nữa.
- IAM Roles và Policies không còn dư thừa.
- GitHub Actions OIDC Role đã được xóa nếu không dùng nữa.
- GitHub Secrets đã được xóa nếu không dùng nữa.
- Billing không còn phát sinh chi phí bất thường.

#### Kết quả đạt được

Sau khi hoàn thành phần cleanup, các tài nguyên AWS của workshop đã được dọn dẹp.

Các kết quả đạt được gồm:

- Đã xóa frontend static files trên S3.
- Đã xóa product images trên S3.
- Đã xóa CloudFormation stack chính.
- Đã xóa hoặc kiểm tra các tài nguyên liên quan như API Gateway, Lambda, DynamoDB, Cognito, CloudFront, EventBridge, CloudWatch và SNS.
- Đã kiểm tra các tài nguyên có thể còn sót lại như SES, Secrets Manager, IAM Roles và GitHub Actions OIDC.
- Đã kiểm tra Billing để đảm bảo không còn chi phí bất thường.

Sau bước này, workshop **Mimi Jewelry E-commerce Platform** đã hoàn tất toàn bộ vòng đời triển khai: chuẩn bị môi trường, triển khai backend, triển khai frontend, authentication, quản lý sản phẩm, đặt hàng, thanh toán, monitoring và cleanup.
