---
title: "Các bước chuẩn bị"
date: 2026-05-11
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### IAM permissions

Gắn IAM permission policy sau vào tài khoản AWS user của bạn để triển khai và dọn dẹp tài nguyên trong workshop này.

Policy này cấp quyền cho các dịch vụ chính được sử dụng trong dự án **AWS Serverless E-commerce Platform**, bao gồm: CloudFormation, S3, CloudFront, Lambda, API Gateway, DynamoDB, Cognito, EventBridge, CloudWatch, SNS, SES, WAF và IAM.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EcommerceWorkshopDeploymentPermissions",
      "Effect": "Allow",
      "Action": [
        "cloudformation:*",

        "s3:CreateBucket",
        "s3:DeleteBucket",
        "s3:ListAllMyBuckets",
        "s3:ListBucket",
        "s3:GetBucketLocation",
        "s3:GetBucketPolicy",
        "s3:PutBucketPolicy",
        "s3:DeleteBucketPolicy",
        "s3:GetBucketPublicAccessBlock",
        "s3:PutBucketPublicAccessBlock",
        "s3:GetBucketWebsite",
        "s3:PutBucketWebsite",
        "s3:DeleteBucketWebsite",
        "s3:GetBucketCors",
        "s3:PutBucketCors",
        "s3:GetBucketVersioning",
        "s3:PutBucketVersioning",
        "s3:GetObject",
        "s3:GetObjectVersion",
        "s3:PutObject",
        "s3:DeleteObject",
        "s3:DeleteObjectVersion",
        "s3:AbortMultipartUpload",
        "s3:ListBucketMultipartUploads",
        "s3:ListMultipartUploadParts",

        "cloudfront:*",

        "lambda:*",
        "apigateway:*",
        "execute-api:*",

        "dynamodb:*",

        "cognito-idp:*",

        "events:*",
        "scheduler:*",

        "logs:*",
        "cloudwatch:*",

        "sns:*",

        "ses:*",

        "wafv2:*",

        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:GetRole",
        "iam:UpdateRole",
        "iam:ListRoles",
        "iam:PassRole",
        "iam:AttachRolePolicy",
        "iam:DetachRolePolicy",
        "iam:PutRolePolicy",
        "iam:GetRolePolicy",
        "iam:DeleteRolePolicy",
        "iam:CreatePolicy",
        "iam:GetPolicy",
        "iam:DeletePolicy",
        "iam:ListPolicyVersions",
        "iam:CreatePolicyVersion",
        "iam:DeletePolicyVersion",
        "iam:TagRole",
        "iam:UntagRole",
        "iam:ListRolePolicies",
        "iam:ListAttachedRolePolicies",
        "iam:CreateOpenIDConnectProvider",
        "iam:GetOpenIDConnectProvider",
        "iam:DeleteOpenIDConnectProvider",
        "iam:ListOpenIDConnectProviders",

        "sts:GetCallerIdentity"
      ],
      "Resource": "*"
    }
  ]
}
```

#### Region sử dụng trong workshop

Trong workshop này, chúng ta sẽ triển khai hệ thống tại region:

```text
ap-southeast-1
```

Region này tương ứng với **Asia Pacific (Singapore)** và phù hợp với người dùng tại Việt Nam vì có độ trễ thấp hơn so với các region xa hơn.

Trước khi bắt đầu, hãy kiểm tra region trong AWS Console và AWS CLI.

```bash
aws configure get region
```

Nếu region chưa đúng, cấu hình lại:

```bash
aws configure
```

Nhập region:

```text
ap-southeast-1
```

#### Kiểm tra AWS account

Trước khi deploy, kiểm tra tài khoản AWS đang sử dụng:

```bash
aws sts get-caller-identity
```

Kết quả mong đợi sẽ có dạng:

```json
{
  "UserId": "AIDAxxxxxxxxxxxxx",
  "Account": "451626662985",
  "Arn": "arn:aws:iam::451626662985:user/your-user"
}
```

Nếu lệnh này chạy thành công nghĩa là AWS CLI đã kết nối được với tài khoản AWS.

#### Cài đặt công cụ cần thiết

Để triển khai dự án, máy tính cần có các công cụ sau:

- **Git** để clone source code từ GitHub.
- **Node.js** để cài dependency và build frontend/backend.
- **AWS CLI** để thao tác với AWS từ terminal.
- **AWS SAM CLI** để build và deploy backend serverless.
- **Visual Studio Code** để chỉnh sửa source code.
- **GitHub account** để lưu source code và cấu hình CI/CD.

Kiểm tra Git:

```bash
git --version
```

Kiểm tra Node.js:

```bash
node -v
```

Kiểm tra npm:

```bash
npm -v
```

Kiểm tra AWS CLI:

```bash
aws --version
```

Kiểm tra SAM CLI:

```bash
sam --version
```

#### Clone source code dự án

Clone source code từ GitHub về máy:

```bash
git clone https://github.com/tun12348573/ecommerce-aws.git
```

<img src="/images/5-Workshop/5.2-Prerequisite/git-clone.png" alt="Git Clone Project" style="max-width: 100%; height: auto;">
Di chuyển vào thư mục dự án:

```bash
cd ecommerce-aws
```

<img src="/images/5-Workshop/5.2-Prerequisite/cd.png" alt="Change Directory" style="max-width: 100%; height: auto;">

Mở project bằng Visual Studio Code:

```bash
code .
```

<img src="/images/5-Workshop/5.2-Prerequisite/open.png" alt="Open Project in VS Code" style="max-width: 100%; height: auto;">

#### Cấu trúc source code

Dự án ecommerce sẽ có cấu trúc chính như sau:

```text
ecommerce-aws/
├── backend/
│   ├── src/
│   ├── template.yaml
│   ├── package.json
│   └── samconfig.toml
│
├── frontend/
│   ├── src/
│   ├── public/
│   ├── package.json
│   └── vite.config.js
│
├── .github/
│   └── workflows/
│
└── README.md
```

Trong đó:

- `backend/` chứa mã nguồn Lambda, API Gateway, DynamoDB, Cognito, EventBridge, CloudWatch và các tài nguyên backend.
- `frontend/` chứa giao diện website bán hàng cho customer và admin.
- `.github/workflows/` chứa workflow CI/CD dùng để tự động deploy hệ thống.
- `template.yaml` là file AWS SAM / CloudFormation dùng để khai báo hạ tầng backend.

#### Chuẩn bị biến môi trường cho backend

Backend cần một số biến môi trường để tích hợp thanh toán, gửi email và cấu hình hệ thống.

Các giá trị mẫu:

```text
AWS_REGION=ap-southeast-1
STAGE=prod
APP_NAME=ecommerce-aws

ZALOPAY_APP_ID=your_zalopay_app_id
ZALOPAY_KEY1=your_zalopay_key1
ZALOPAY_KEY2=your_zalopay_key2
ZALOPAY_ENDPOINT=https://sb-openapi.zalopay.vn/v2/create

ALERT_EMAIL=your_email@example.com
```

Lưu ý:

- Không commit secret thật lên GitHub.
- Các key như `ZALOPAY_KEY1`, `ZALOPAY_KEY2` nên được lưu bằng GitHub Secrets hoặc AWS Parameter Store / Secrets Manager nếu triển khai production.
- Trong workshop có thể dùng ZaloPay Sandbox để mô phỏng thanh toán.

#### Chuẩn bị ZaloPay Sandbox

Dự án sử dụng **ZaloPay Sandbox** để mô phỏng thanh toán online.

Cần chuẩn bị:

- ZaloPay Sandbox App ID.
- Key 1.
- Key 2.
- Endpoint sandbox.
- Callback URL sau khi backend được deploy.

Luồng thanh toán trong dự án:

```text
Customer Checkout
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

Sau khi deploy backend xong, callback URL sẽ có dạng:

```text
https://your-api-id.execute-api.ap-southeast-1.amazonaws.com/prod/payment/zalopay/callback
```

#### Chuẩn bị email cảnh báo SNS

Hệ thống sử dụng **Amazon SNS** để gửi email cảnh báo khi có lỗi hoặc metric bất thường.

Email nhận cảnh báo trong workshop có thể là:

```text
namdao110999@gmail.com
```

Sau khi deploy stack, AWS sẽ gửi email xác nhận subscription từ SNS.

Bạn cần mở email và bấm:

```text
Confirm subscription
```

<img src="/images/5-Workshop/5.2-Prerequisite/confirm-sub.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

Nếu chưa xác nhận subscription, SNS sẽ chưa gửi được cảnh báo.

#### Chuẩn bị SES cho transactional email

Dự án có thể sử dụng **Amazon SES** để gửi email giao dịch, ví dụ:

- Email xác nhận đơn hàng.
- Email thông báo thanh toán thành công.
- Email thông báo đơn hàng bị hủy.
- Email thông báo hoàn tiền.

Trong môi trường sandbox, SES yêu cầu verify email hoặc domain trước khi gửi.

Các bước chuẩn bị:

- Vào Amazon SES.
- Chọn region `ap-southeast-1`.
- Vào Identities -> Create Identity.
- Ở phần Identity type chọn Email Address, nhập Email bạn muốn dùng để gửi mail
- Bấm Create identity
- Kiểm tra email xác nhận từ AWS.
- Bấm xác nhận verify.

<img src="/images/5-Workshop/5.2-Prerequisite/create-identity.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

<img src="/images/5-Workshop/5.2-Prerequisite/email-verify.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

Lưu ý:

- Nếu SES vẫn ở sandbox mode, bạn chỉ gửi được email đến các địa chỉ đã verify.
- Để gửi email production, cần request production access.
- Nếu AWS yêu cầu verify domain trước, bạn cần hoàn tất xác minh domain trong SES.

#### Build backend bằng AWS SAM

Di chuyển vào thư mục backend:

```bash
cd .\backend\
```

Cài dependency:

```bash
npm install
```

<img src="/images/5-Workshop/5.2-Prerequisite/npm.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

Build backend:

```bash
cd ../infrastructure
sam build
```

Kết quả mong đợi:

```text
Build Succeeded
```

<img src="/images/5-Workshop/5.2-Prerequisite/sam.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

#### Deploy backend bằng AWS SAM

Deploy backend lần đầu:

```bash
sam deploy --guided
```

<img src="/images/5-Workshop/5.2-Prerequisite/deploy.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

Các giá trị đề xuất:

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

Sau khi xác nhận, SAM sẽ tạo các tài nguyên AWS thông qua CloudFormation.

Các tài nguyên chính được tạo gồm:

- API Gateway HTTP API.
- Lambda backend function.
- Lambda scheduled functions.
- DynamoDB Products table.
- DynamoDB Orders table.
- DynamoDB Refunds table.
- Cognito User Pools.
- S3 Frontend Bucket.
- S3 Product Images Bucket.
- CloudFront Distribution.
- EventBridge Rules.
- CloudWatch Logs.
- CloudWatch Alarms.
- SNS Topic.
- IAM Roles và Policies.

Quá trình deploy có thể mất vài phút.

#### Kiểm tra CloudFormation stack

Sau khi deploy, vào AWS Console:

```text
CloudFormation → Stacks → ecommerce-aws-backend
```

Trạng thái mong đợi:

```text
CREATE_COMPLETE
```

Nếu đã deploy trước đó, trạng thái có thể là:

```text
UPDATE_COMPLETE
```

<img src="/static/images/5-Workshop/5.2-Prerequisite/done.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

#### Lấy thông tin output sau khi deploy

Sau khi CloudFormation deploy thành công, mở tab **Outputs** để lấy các thông tin quan trọng như:

- API Gateway URL.
- CloudFront URL.
- Frontend Bucket Name.
- Product Images Bucket Name.
- Cognito User Pool ID.
- Cognito App Client ID.

Ví dụ:

```text
API URL:
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod

CloudFront URL:
https://dfjng6e5jz4mf.cloudfront.net
```

<img src="/images/5-Workshop/5.2-Prerequisite/output.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

#### Cấu hình frontend

Di chuyển vào thư mục frontend:

```bash
cd ../frontend
```

Cài dependency:

```bash
npm install
```

<img src="/images/5-Workshop/5.2-Prerequisite/frontend-npm.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

Tạo file môi trường cho frontend:

```text
frontend/.env.local
```

<img src="/images/5-Workshop/5.2-Prerequisite/tao-env.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

Nội dung mẫu:

```env
VITE_API_BASE_URL=https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod

VITE_AWS_REGION=ap-southeast-1

VITE_CUSTOMER_USER_POOL_ID=ap-southeast-1_LHSScZri7
VITE_CUSTOMER_USER_POOL_CLIENT_ID=1uijbf07brda1p130293v3prfv

VITE_ADMIN_USER_POOL_ID=ap-southeast-1_k57hCsM4z
VITE_ADMIN_USER_POOL_CLIENT_ID=1qc3ttu4q3e95pk7mlqsivr66e

VITE_PRODUCT_IMAGES_BASE_URL=https://dfjng6e5jz4mf.cloudfront.net/uploads
```

Lưu ý:

- Thay các giá trị bằng output thực tế từ CloudFormation.
- Không đưa secret vào file `.env` của frontend.
- Frontend chỉ nên chứa các thông tin public như API URL, region, Cognito User Pool ID và Client ID.

#### Chạy frontend local

Chạy frontend trên máy local để kiểm tra:

```bash
npm run dev
```

<img src="/images/5-Workshop/5.2-Prerequisite/run-dev.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

Mở trình duyệt:

```text
http://localhost:3000
```

Kiểm tra các chức năng:

- Trang chủ hiển thị.
- Danh sách sản phẩm hiển thị.
- Đăng ký / đăng nhập hoạt động.
- Gọi API Gateway thành công.
- Admin có thể đăng nhập.
- Cart và checkout hoạt động.

#### Build frontend

Build frontend production:

```bash
npm run build
```

<img src="/images/5-Workshop/5.2-Prerequisite/frontend-build.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

Kết quả build sẽ nằm trong thư mục:

```text
frontend/dist
```

#### Upload frontend lên S3

Sau khi build frontend, upload thư mục `dist` lên S3 Frontend Bucket.

Ví dụ:

```bash
aws s3 sync dist/ s3://ecommerce-aws-backend-frontendbucket-9wu7aid8okd8 --delete
```

Lưu ý:

- Thay bucket name bằng bucket frontend thực tế của bạn.
- Thư mục upload là `dist/`.

#### Tạo CloudFront invalidation

Sau khi upload frontend lên S3, tạo CloudFront invalidation để CloudFront lấy bản mới nhất:

```bash
aws cloudfront create-invalidation --distribution-id E3EAY8XN54SSDX --paths "/*"
```

Sau vài phút, mở CloudFront URL để kiểm tra website:

```text
https://dfjng6e5jz4mf.cloudfront.net
```

#### Chuẩn bị GitHub Actions CI/CD

Dự án có thể dùng GitHub Actions để tự động deploy khi push code lên branch `main`.

Cần chuẩn bị:

- GitHub repository.
- AWS IAM OIDC Provider.
- IAM Role cho GitHub Actions.
- GitHub Secrets nếu cần.
- Workflow file trong `.github/workflows/`.

Các secret có thể cần:

```text
AWS_REGION
AWS_ROLE_TO_ASSUME
ZALOPAY_APP_ID
ZALOPAY_KEY1
ZALOPAY_KEY2
```

Luồng CI/CD:

```text
Developer Push Code
   ↓
GitHub Actions
   ↓
Assume AWS IAM Role using OIDC
   ↓
SAM Build & Deploy Backend
   ↓
Build Frontend
   ↓
Sync Frontend to S3
   ↓
Create CloudFront Invalidation
```

#### Kiểm tra tài nguyên sau khi deploy

Sau khi hoàn tất deploy, kiểm tra các dịch vụ sau trong AWS Console:

- **CloudFormation**: stack ở trạng thái `CREATE_COMPLETE` hoặc `UPDATE_COMPLETE`.
- **S3**: có Frontend Bucket và Product Images Bucket.
- **CloudFront**: distribution đã enabled.
- **API Gateway**: API endpoint hoạt động.
- **Lambda**: các function đã được tạo.
- **DynamoDB**: các bảng Products, Orders, Refunds đã được tạo.
- **Cognito**: customer và admin user pools đã được tạo.
- **EventBridge**: scheduled rules đã được enabled.
- **CloudWatch Logs**: Lambda có log group.
- **SNS**: email subscription đã được confirm.

#### Kết quả sau bước chuẩn bị

Sau khi hoàn thành phần chuẩn bị, bạn sẽ có:

- AWS CLI đã được cấu hình.
- SAM CLI đã sẵn sàng để deploy.
- Source code đã được clone về máy.
- IAM permission đã được gắn cho user triển khai.
- Backend đã được deploy bằng SAM / CloudFormation.
- Frontend đã được build và upload lên S3.
- CloudFront đã phân phối website.
- API Gateway đã kết nối với Lambda.
- DynamoDB đã có các bảng cần thiết.
- Cognito đã có user pool cho customer và admin.
- SNS email alert đã được xác nhận.
- ZaloPay Sandbox đã sẵn sàng để test thanh toán.
- GitHub Actions đã sẵn sàng để cấu hình CI/CD.

Sau bước này, bạn có thể chuyển sang phần thực hành triển khai và kiểm thử các chức năng chính của hệ thống ecommerce.
