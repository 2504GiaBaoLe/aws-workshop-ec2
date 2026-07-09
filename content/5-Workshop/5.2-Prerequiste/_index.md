---
title: "Preparation Steps"
date: 2026-05-11
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### IAM permissions

Attach the following IAM permission policy to your AWS user account to deploy and clean up the resources used in this workshop.

This policy grants permissions for the main services used in the **AWS Serverless E-commerce Platform** project, including CloudFormation, S3, CloudFront, Lambda, API Gateway, DynamoDB, Cognito, EventBridge, CloudWatch, SNS, SES, WAF, and IAM.

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

#### AWS Region used in this workshop

In this workshop, we will deploy the system in the following Region:

```text
ap-southeast-1
```

This Region is **Asia Pacific (Singapore)** and is suitable for users in Vietnam because it provides lower latency compared to farther Regions.

Before starting, check the Region in AWS Console and AWS CLI.

```bash
aws configure get region
```

If the Region is not correct, configure it again:

```bash
aws configure
```

Enter the Region:

```text
ap-southeast-1
```

#### Check AWS account

Before deploying, check the AWS account currently being used:

```bash
aws sts get-caller-identity
```

Expected result:

```json
{
  "UserId": "AIDAxxxxxxxxxxxxx",
  "Account": "451626662985",
  "Arn": "arn:aws:iam::451626662985:user/your-user"
}
```

If this command runs successfully, it means AWS CLI is connected to your AWS account.

#### Install required tools

To deploy the project, your computer needs the following tools:

- **Git** to clone the source code from GitHub.
- **Node.js** to install dependencies and build the frontend/backend.
- **AWS CLI** to interact with AWS from the terminal.
- **AWS SAM CLI** to build and deploy the serverless backend.
- **Visual Studio Code** to edit the source code.
- **GitHub account** to store the source code and configure CI/CD.

Check Git:

```bash
git --version
```

Check Node.js:

```bash
node -v
```

Check npm:

```bash
npm -v
```

Check AWS CLI:

```bash
aws --version
```

Check SAM CLI:

```bash
sam --version
```

#### Clone the project source code

Clone the source code from GitHub to your computer:

```bash
git clone https://github.com/tun12348573/ecommerce-aws.git
```

<img src="/images/5-Workshop/5.2-Prerequisite/git-clone.png" alt="Git Clone Project" style="max-width: 100%; height: auto;">

Move into the project folder:

```bash
cd ecommerce-aws
```

<img src="/images/5-Workshop/5.2-Prerequisite/cd.png" alt="Change Directory" style="max-width: 100%; height: auto;">

Open the project with Visual Studio Code:

```bash
code .
```

<img src="/images/5-Workshop/5.2-Prerequisite/open.png" alt="Open Project in VS Code" style="max-width: 100%; height: auto;">

#### Source code structure

The e-commerce project has the following main structure:

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

Where:

- `backend/` contains Lambda source code, API Gateway, DynamoDB, Cognito, EventBridge, CloudWatch, and backend resources.
- `frontend/` contains the customer and admin shopping website interface.
- `.github/workflows/` contains CI/CD workflows used to automatically deploy the system.
- `template.yaml` is the AWS SAM / CloudFormation file used to define backend infrastructure.

#### Prepare backend environment variables

The backend needs several environment variables for payment integration, email sending, and system configuration.

Sample values:

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

Notes:

- Do not commit real secrets to GitHub.
- Keys such as `ZALOPAY_KEY1` and `ZALOPAY_KEY2` should be stored in GitHub Secrets or AWS Parameter Store / Secrets Manager for production deployment.
- In this workshop, ZaloPay Sandbox can be used to simulate online payment.

#### Prepare ZaloPay Sandbox

The project uses **ZaloPay Sandbox** to simulate online payment.

You need to prepare:

- ZaloPay Sandbox App ID.
- Key 1.
- Key 2.
- Sandbox endpoint.
- Callback URL after the backend is deployed.

Payment flow in this project:

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

After the backend is deployed, the callback URL will look like this:

```text
https://your-api-id.execute-api.ap-southeast-1.amazonaws.com/prod/payment/zalopay/callback
```

#### Prepare SNS alert email

The system uses **Amazon SNS** to send alert emails when errors or abnormal metrics occur.

The alert email used in this workshop can be:

```text
namdao110999@gmail.com
```

After deploying the stack, AWS will send a subscription confirmation email from SNS.

You need to open the email and click:

```text
Confirm subscription
```

<img src="/images/5-Workshop/5.2-Prerequisite/confirm-sub.png" alt="Confirm SNS Subscription" style="max-width: 100%; height: auto;">

If the subscription is not confirmed, SNS will not be able to send alert emails.

#### Prepare SES for transactional emails

The project can use **Amazon SES** to send transactional emails, for example:

- Order confirmation email.
- Successful payment notification email.
- Cancelled order notification email.
- Refund notification email.

In the sandbox environment, SES requires email or domain verification before sending emails.

Preparation steps:

- Open Amazon SES.
- Select Region `ap-southeast-1`.
- Go to Identities -> Create Identity.
- In Identity type, select Email Address and enter the email address you want to use for sending emails.
- Click Create identity.
- Check the verification email from AWS.
- Click the verification link.

<img src="/images/5-Workshop/5.2-Prerequisite/create-identity.png" alt="Create SES Identity" style="max-width: 100%; height: auto;">

<img src="/images/5-Workshop/5.2-Prerequisite/email-verify.png" alt="SES Email Verification" style="max-width: 100%; height: auto;">

Notes:

- If SES is still in sandbox mode, you can only send emails to verified addresses.
- To send production emails, you need to request production access.
- If AWS requires domain verification first, you need to complete domain verification in SES.

#### Build backend with AWS SAM

Move into the backend folder:

```bash
cd .\backend\
```

Install dependencies:

```bash
npm install
```

<img src="/images/5-Workshop/5.2-Prerequisite/npm.png" alt="Install Backend Dependencies" style="max-width: 100%; height: auto;">

Build the backend:

```bash
cd ../infrastructure
sam build
```

Expected result:

```text
Build Succeeded
```

<img src="/images/5-Workshop/5.2-Prerequisite/sam.png" alt="SAM Build Success" style="max-width: 100%; height: auto;">

#### Deploy backend with AWS SAM

Deploy the backend for the first time:

```bash
sam deploy --guided
```

<img src="/images/5-Workshop/5.2-Prerequisite/deploy.png" alt="SAM Deploy Guided" style="max-width: 100%; height: auto;">

Recommended values:

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

After confirmation, SAM will create AWS resources through CloudFormation.

The main resources created include:

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
- IAM Roles and Policies.

The deployment process may take a few minutes.

#### Check CloudFormation stack

After deployment, open AWS Console:

```text
CloudFormation → Stacks → ecommerce-aws-backend
```

Expected status:

```text
CREATE_COMPLETE
```

If the stack has been deployed before, the status may be:

```text
UPDATE_COMPLETE
```

<img src="/images/5-Workshop/5.2-Prerequisite/done.png" alt="CloudFormation Stack Complete" style="max-width: 100%; height: auto;">

#### Get output information after deployment

After CloudFormation deployment is complete, open the **Outputs** tab to get important information such as:

- API Gateway URL.
- CloudFront URL.
- Frontend Bucket Name.
- Product Images Bucket Name.
- Cognito User Pool ID.
- Cognito App Client ID.

Example:

```text
API URL:
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod

CloudFront URL:
https://dfjng6e5jz4mf.cloudfront.net
```

<img src="/images/5-Workshop/5.2-Prerequisite/output.png" alt="CloudFormation Outputs" style="max-width: 100%; height: auto;">

#### Configure frontend

Move into the frontend folder:

```bash
cd ../frontend
```

Install dependencies:

```bash
npm install
```

<img src="/images/5-Workshop/5.2-Prerequisite/frontend-npm.png" alt="Install Frontend Dependencies" style="max-width: 100%; height: auto;">

Create the frontend environment file:

```text
frontend/.env.local
```

<img src="/images/5-Workshop/5.2-Prerequisite/tao-env.png" alt="Create Frontend Environment File" style="max-width: 100%; height: auto;">

Sample content:

```env
VITE_API_BASE_URL=https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod

VITE_AWS_REGION=ap-southeast-1

VITE_CUSTOMER_USER_POOL_ID=ap-southeast-1_LHSScZri7
VITE_CUSTOMER_USER_POOL_CLIENT_ID=1uijbf07brda1p130293v3prfv

VITE_ADMIN_USER_POOL_ID=ap-southeast-1_k57hCsM4z
VITE_ADMIN_USER_POOL_CLIENT_ID=1qc3ttu4q3e95pk7mlqsivr66e

VITE_PRODUCT_IMAGES_BASE_URL=https://dfjng6e5jz4mf.cloudfront.net/uploads
```

Notes:

- Replace the values with the actual outputs from CloudFormation.
- Do not put secrets in the frontend `.env` file.
- The frontend should only contain public values such as API URL, Region, Cognito User Pool ID, and Client ID.

#### Run frontend locally

Run the frontend locally for testing:

```bash
npm run dev
```

<img src="/images/5-Workshop/5.2-Prerequisite/run-dev.png" alt="Run Frontend Locally" style="max-width: 100%; height: auto;">

Open the browser:

```text
http://localhost:3000
```

Check the following features:

- Homepage is displayed.
- Product list is displayed.
- Register / login works.
- API Gateway can be called successfully.
- Admin can log in.
- Cart and checkout work.

#### Build frontend

Build the production frontend:

```bash
npm run build
```

<img src="/images/5-Workshop/5.2-Prerequisite/frontend-build.png" alt="Frontend Build" style="max-width: 100%; height: auto;">

The build output will be located in:

```text
frontend/dist
```

#### Upload frontend to S3

After building the frontend, upload the `dist` folder to the S3 Frontend Bucket.

Example:

```bash
aws s3 sync dist/ s3://ecommerce-aws-backend-frontendbucket-9wu7aid8okd8 --delete
```

Notes:

- Replace the bucket name with your actual frontend bucket.
- The upload folder is `dist/`.

#### Create CloudFront invalidation

After uploading the frontend to S3, create a CloudFront invalidation so CloudFront can serve the latest version:

```bash
aws cloudfront create-invalidation --distribution-id E3EAY8XN54SSDX --paths "/*"
```

After a few minutes, open the CloudFront URL to check the website:

```text
https://dfjng6e5jz4mf.cloudfront.net
```

#### Prepare GitHub Actions CI/CD

The project can use GitHub Actions to automatically deploy when code is pushed to the `main` branch.

You need to prepare:

- GitHub repository.
- AWS IAM OIDC Provider.
- IAM Role for GitHub Actions.
- GitHub Secrets if needed.
- Workflow file in `.github/workflows/`.

Possible secrets:

```text
AWS_REGION
AWS_ROLE_TO_ASSUME
ZALOPAY_APP_ID
ZALOPAY_KEY1
ZALOPAY_KEY2
```

CI/CD flow:

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

#### Check resources after deployment

After deployment is complete, check the following services in AWS Console:

- **CloudFormation**: stack status is `CREATE_COMPLETE` or `UPDATE_COMPLETE`.
- **S3**: Frontend Bucket and Product Images Bucket exist.
- **CloudFront**: distribution is enabled.
- **API Gateway**: API endpoint works.
- **Lambda**: functions are created.
- **DynamoDB**: Products, Orders, and Refunds tables are created.
- **Cognito**: customer and admin user pools are created.
- **EventBridge**: scheduled rules are enabled.
- **CloudWatch Logs**: Lambda log groups exist.
- **SNS**: email subscription is confirmed.

#### Result after preparation

After completing the preparation steps, you will have:

- AWS CLI configured.
- SAM CLI ready for deployment.
- Source code cloned to your local machine.
- IAM permissions attached to the deployment user.
- Backend deployed using SAM / CloudFormation.
- Frontend built and uploaded to S3.
- CloudFront distributing the website.
- API Gateway connected to Lambda.
- DynamoDB tables created.
- Cognito user pools created for customers and admins.
- SNS email alert subscription confirmed.
- ZaloPay Sandbox ready for payment testing.
- GitHub Actions ready for CI/CD configuration.

After this step, you can move to the implementation and testing sections of the e-commerce system.
