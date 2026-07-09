---
title: "Backend Deployment"
date: 2026-05-11
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Objective

In this section, we will deploy the backend of the **Mimi Jewelry E-commerce Platform** project using **AWS SAM** and **AWS CloudFormation**.

The backend is the most important part of the system because it creates the main resources such as:

- Amazon API Gateway to receive requests from the frontend.
- AWS Lambda to handle business logic.
- Amazon DynamoDB to store products, orders, and refund data.
- Amazon Cognito to authenticate customers and admins.
- Amazon S3 to store frontend files and product images.
- Amazon CloudFront to distribute the website.
- Amazon EventBridge to run scheduled tasks.
- Amazon CloudWatch to store logs and metrics.
- Amazon SNS to send alert emails.
- IAM Roles and Policies for AWS services.

#### Backend architecture

The backend of this project is designed using a serverless architecture. The frontend sends requests to API Gateway, then API Gateway invokes Lambda to process the logic. Lambda reads and writes data to DynamoDB, generates presigned URLs to upload images to S3, handles payment callbacks from ZaloPay, and writes logs to CloudWatch.

General backend processing flow:

```text
Frontend
   ↓
Amazon API Gateway
   ↓
AWS Lambda
   ↓
Amazon DynamoDB
```

Product image upload flow:

```text
Admin Frontend
   ↓
API Gateway
   ↓
Lambda generates presigned URL
   ↓
Amazon S3 Product Images Bucket
```

Background task processing flow:

```text
Amazon EventBridge
   ↓
Scheduled Lambda Function
   ↓
Amazon DynamoDB
   ↓
Amazon CloudWatch Logs
```

#### Move to the backend folder

Open the terminal at the project directory:

```text
C:\Users\namda\ecommerce-aws
```

Then move to the backend folder:

```bash
cd backend
```

Check the main files in the backend folder:

```text
backend/
├── src/
├──amda\ecommerce-aws
```

Then move to the backend folder:

```bash
cd backend
```

Check the main files template.yaml
├── package.json
├── tsconfig.json
└── samconfig.toml

````

Where:

- `src/` contains the Lambda source code.
- `template.yaml` is the file used to define AWS infrastructure with AWS SAM.
- `package.json` contains build scripts and dependencies.
- `tsconfig.json` is used to configure TypeScript.
- `samconfig.toml` stores deployment configuration after running `sam deploy --guided`.

#### Check AWS CLI

Before deployment, check whether AWS CLI is logged in to the correct account:

```bash
aws sts get-caller-identity
````

Expected result:

```json
{
  "UserId": "AIDAxxxxxxxxxxxxx",
  "Account": "451626662985",
  "Arn": "arn:aws:iam::451626662985:user/your-user"
}
```

Check the current Region:

```bash
aws configure get region
```

The Region used in this workshop is:

```text
ap-southeast-1
```

<img src="/images/5-Workshop/5.3-Backend-deployment/account.png" alt="AWS Serverless E-commerce Architecture" style="max-width: 100%; height: auto;">

If the Region is not correct, configure it again:

```bash
aws configure
```

#### Install backend dependencies

Inside the `backend` folder, run:

```bash
npm install
```

This command installs the required libraries to build and deploy the backend.

After the installation is complete, check that the `node_modules` folder has been created:

```text
backend/node_modules/
```

#### Check template.yaml

The `template.yaml` file is the most important file of the backend. This file defines the entire serverless infrastructure of the project.

The main resources in `template.yaml` include:

- `EcommerceBackendFunction`: the main Lambda function that handles API requests.
- `RefundReconciliationFunction`: Lambda function for refund reconciliation.
- `PaymentExpirySchedulerFunction`: Lambda function for expired payment handling.
- `HttpApi`: API Gateway HTTP API.
- `ProductsTable`: DynamoDB table for storing products.
- `OrdersTable`: DynamoDB table for storing orders.
- `CustomerUserPool`: Cognito User Pool for customers.
- `AdminUserPool`: Cognito User Pool for admins.
- `FrontendBucket`: S3 bucket for frontend static files.
- `ProductImagesBucket`: S3 bucket for product images.
- `CloudFrontDistribution`: CloudFront distribution for the website.
- `SNSTopic`: SNS topic for sending alerts.
- `CloudWatchAlarms`: operational alarms for the system.

#### Build backend with AWS SAM

Run the following command inside the `infrastructure` folder:

```bash
sam build
```

AWS SAM will read the `template.yaml` file, build the Lambda source code, and prepare artifacts for deployment to AWS.

Expected result:

```text
Build Succeeded
```

If the build is successful, SAM will create the following folder:

```text
infrastructure/.aws-sam/
```

This folder contains the built artifacts and will be used in the deployment step.

#### Deploy backend for the first time with SAM

Run the command:

```bash
sam deploy --guided
```

When SAM asks for information, enter the following values:

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

Explanation:

- `Stack Name` is the name of the CloudFormation stack.
- `AWS Region` is the Region where the system will be deployed.
- `Confirm changes before deploy` allows you to review changes before deployment.
- `Allow SAM CLI IAM role creation` allows SAM to create IAM Roles for Lambda.
- `Save arguments to configuration file` saves the configuration to `samconfig.toml` so the next deployments are faster.

#### Confirm deployment

After running `sam deploy --guided`, SAM will display the list of resources that will be created or updated.

If everything looks correct, enter:

```text
y
```

to confirm the deployment.

The deployment process may take a few minutes because CloudFormation needs to create multiple resources such as Lambda, API Gateway, DynamoDB, Cognito, S3, CloudFront, and IAM Roles.

#### Deploy again after samconfig.toml is created

After the first deployment, you only need to run:

```bash
sam build
sam deploy
```

You do not need to enter all configuration values again because SAM has already saved them in:

```text
backend/samconfig.toml
```

#### Check CloudFormation stack

After the deployment is complete, open AWS Console:

```text
CloudFormation → Stacks
```

Select the stack:

```text
ecommerce-aws-backend
```

Expected status:

```text
CREATE_COMPLETE
```

For later updates, the status may be:

```text
UPDATE_COMPLETE
```

<img src="/images/5-Workshop/5.3-Backend-deployment/done.png" alt="CloudFormation Stack Complete" style="max-width: 100%; height: auto;">

#### Check stack Outputs

In the CloudFormation stack, open the following tab:

```text
Outputs
```

Here, you will see important information such as:

- API Gateway URL.
- CloudFront URL.
- Frontend Bucket Name.
- Product Images Bucket Name.
- Customer User Pool ID.
- Customer User Pool Client ID.
- Admin User Pool ID.
- Admin User Pool Client ID.

Example:

```text
ApiUrl:
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod

CloudFrontUrl:
https://dfjng6e5jz4mf.cloudfront.net
```

These values will be used in the frontend configuration section.

<img src="/images/5-Workshop/5.3-Backend-deployment/output.png" alt="CloudFormation Outputs" style="max-width: 100%; height: auto;">

#### Check API Gateway

Open AWS Console:

```text
API Gateway → APIs
```

Check that the API has been created.

This API is used by the frontend to call endpoints such as:

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

API Gateway acts as the connection point between the frontend and the Lambda backend.

<img src="/images/5-Workshop/5.3-Backend-deployment/api-gateway.png/" alt="API Gateway" style="max-width: 100%; height: auto;">

#### Check Lambda functions

Open AWS Console:

```text
Lambda → Functions
```

After deployment, there will be several main Lambda functions:

- The main backend Lambda function to handle requests from API Gateway.
- Lambda function to process expired unpaid orders.
- Lambda function to reconcile refund status.

These Lambda functions are automatically created from `template.yaml`.

<img src="/images/5-Workshop/5.3-Backend-deployment/lambda-function.png" alt="Lambda Functions" style="max-width: 100%; height: auto;">

#### Check DynamoDB tables

Open AWS Console:

```text
DynamoDB → Tables
```

The main tables of the project include:

- `EcommerceProducts`: stores product information.
- `EcommerceOrders`: stores order information.
- Tables or indexes related to refunds if they are defined in the template.

In the Orders table, the system uses Global Secondary Indexes to optimize queries by customer and refund status.

Example indexes:

```text
OrdersByCustomerIndex
RefundsByStatusIndex
```

<img src="/images/5-Workshop/5.3-Backend-deployment/dynamodb-tables.png" alt="DynamoDB Tables" style="max-width: 100%; height: auto;">

#### Check Cognito User Pools

Open AWS Console:

```text
Amazon Cognito → User pools
```

The project uses Cognito for authentication:

- Customers can register, log in, and view their own orders.
- Admins can log in and access the admin dashboard.

Usually, the system will have:

- Customer User Pool.
- Admin User Pool.

<img src="/images/5-Workshop/5.3-Backend-deployment/cognito.png" alt="Cognito User Pools" style="max-width: 100%; height: auto;">

#### Check S3 buckets

Open AWS Console:

```text
Amazon S3 → Buckets
```

After deployment, the system will have the following main buckets:

- Frontend bucket: used to store static files of the website.
- Product images bucket: used to store product images.

The frontend bucket will be distributed to the Internet through CloudFront. The product images bucket is used to store images uploaded from the admin page.

<img src="/images/5-Workshop/5.3-Backend-deployment/s3-buckets.png" alt="S3 Buckets" style="max-width: 100%; height: auto;">


#### Check CloudFront Distribution

Open AWS Console:

```text
CloudFront → Distributions
```

Check that the distribution has been created and its status is:

```text
Enabled
```

CloudFront is used to:

- Distribute the frontend website.
- Cache static content.
- Improve access speed.
- Work with the S3 frontend bucket.
- Support URL rewrite for the static export of Next.js.

<img src="/images/5-Workshop/5.3-Backend-deployment/cloudfront.png" alt="CloudFront Distribution" style="max-width: 100%; height: auto;">

#### Check EventBridge scheduled rules

Open AWS Console:

```text
Amazon EventBridge → Scheduler
```

or:

```text
Amazon EventBridge → Rules
```

Depending on how the template is defined, the system will have scheduled jobs such as:

- Checking expired unpaid ZaloPay orders.
- Reconciling refund status.

These jobs help the system automatically process background tasks without running a server continuously.

<img src="/images/5-Workshop/5.3-Backend-deployment/eventbridge.png" alt="EventBridge Schedules" style="max-width: 100%; height: auto;">

#### Check CloudWatch Logs

Open AWS Console:

```text
CloudWatch → Logs → Log groups
```

Each Lambda function will have its own log group.

Example:

```text
/aws/lambda/ecommerce-aws-backend-...
```

CloudWatch Logs helps check:

- Requests sent to Lambda.
- API processing errors.
- Payment errors.
- Image upload errors.
- Scheduled job errors.
- Processing time and debug information.

<img src="/images/5-Workshop/5.3-Backend-deployment/cloudwatch.png" alt="CloudWatch Log Groups" style="max-width: 100%; height: auto;">

#### Check SNS Topic

Open AWS Console:

```text
Amazon SNS → Topics
```

SNS Topic is used to send alert emails when CloudWatch Alarm detects errors or abnormal metrics.

After deployment, check whether the email subscription has been confirmed.

Expected status:

```text
Confirmed
```

If the status is still:

```text
Pending confirmation
```

open the email and click **Confirm subscription**.

<img src="/images/5-Workshop/5.3-Backend-deployment/sns.png" alt="SNS Topic" style="max-width: 100%; height: auto;">

#### Test public API endpoint

After the backend is deployed successfully, you can test the public API using a browser or Postman.

Example product list endpoint:

```text
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod/products
```

Expected result may be a product list or an empty array if there is no data yet:

```json
[]
```

If the API returns valid data or a valid response, it means API Gateway has successfully invoked Lambda.

#### Test Lambda with CloudWatch Logs

After calling the API, open CloudWatch Logs of the backend Lambda function.

Check the latest log stream to confirm that Lambda received the request.

Logs usually contain information such as:

```text
HTTP method
Request path
Status code
Error message if any
```

Checking logs helps identify errors when the API does not return the expected result.

#### Common errors when deploying backend

##### Missing IAM permission

If you see an error like:

```text
AccessDenied
```

it means the deployment user does not have enough permissions.

How to fix:

- Check whether the IAM policy has been attached to the user.
- Check the `iam:PassRole` permission.
- Check permissions for CloudFormation, Lambda, API Gateway, DynamoDB, S3, and Cognito.

##### Stack rollback error

If CloudFormation shows:

```text
ROLLBACK_IN_PROGRESS
ROLLBACK_COMPLETE
```

open the **Events** tab in CloudFormation to see which resource failed.

After finding the cause, fix `template.yaml` or deployment parameters, then deploy again.

##### Bucket name already exists

S3 bucket names must be globally unique. If the template uses a fixed bucket name and that name already exists, deployment may fail.

How to fix:

- Change the bucket name.
- Or let CloudFormation generate the bucket name automatically.

##### CloudFront deployment takes a long time

CloudFront Distribution may take a few minutes to change to the enabled state. This is normal because CloudFront needs to distribute the configuration to edge locations.

#### Result

After completing this section, you have successfully deployed the serverless backend for the e-commerce project.

The main resources created include:

- API Gateway HTTP API.
- Lambda backend function.
- Scheduled Lambda functions.
- DynamoDB tables and indexes.
- Cognito User Pools for customers and admins.
- S3 buckets for frontend and product images.
- CloudFront Distribution.
- EventBridge scheduled jobs.
- CloudWatch Logs and CloudWatch Alarms.
- SNS Topic for alerts.
- IAM Roles and Policies.

The backend is now ready for the frontend to connect and call APIs. In the next section, we will deploy the frontend, configure environment variables, build the Next.js app, and upload the website to Amazon S3.
