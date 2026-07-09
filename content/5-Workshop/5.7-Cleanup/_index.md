---
title: "Cleanup"
date: 2026-05-11
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

#### Objective

In this section, we will clean up the AWS resources created during the **Mimi Jewelry E-commerce Platform** workshop.

Cleaning up resources after completing the workshop is important to avoid unnecessary AWS charges.

The main contents of this section include:

- Review the resources created during the workshop.
- Back up important data if needed.
- Empty S3 buckets before deleting the stack.
- Delete the CloudFormation stack using AWS SAM.
- Check for remaining resources.
- Delete GitHub Actions OIDC resources if they are no longer used.
- Delete or disable related services such as SES, SNS, and CloudWatch Alarms.
- Check AWS Billing after cleanup.

#### Resources to clean up

In this workshop, the system created multiple AWS resources, including:

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
- IAM Roles and Policies.
- AWS CloudFormation Stack.

Main stack information:

```text
CloudFormation Stack Name:
ecommerce-aws-backend

AWS Region:
ap-southeast-1
```

#### Notes before cleanup

Cleanup will delete system resources and data.

Before continuing, check the following:

- Do you need to keep product data?
- Do you need to keep order data?
- Do you need to keep product images?
- Do you need to keep Cognito users?
- Do you need to keep source code or configuration files?

If you need to keep any data, back it up before deleting the stack.

#### Check CloudFormation stack

Open AWS Console:

```text
CloudFormation → Stacks
```

Select the stack:

```text
ecommerce-aws-backend
```

Check the following tabs:

```text
Resources
Outputs
Events
```

The **Resources** tab shows which resources were created by the stack.

#### Back up DynamoDB data if needed

If you want to keep product or order data, export or scan the data before deletion.

Main tables:

```text
EcommerceProducts
EcommerceOrders
```

You can check the data in AWS Console:

```text
DynamoDB → Tables → EcommerceProducts → Explore table items
DynamoDB → Tables → EcommerceOrders → Explore table items
```

If you do not need to keep the data, you can skip this step.

#### Check S3 buckets

Open AWS Console:

```text
Amazon S3 → Buckets
```

Main buckets in this workshop:

```text
ecommerce-aws-backend-frontendbucket-9wu7aid8okd8
ecommerce-aws-backend-productimagesbucket-eqn2cddpurel
```

The frontend bucket stores static website files.

The product images bucket stores uploaded product images.

#### Empty S3 Frontend Bucket

Before deleting the CloudFormation stack, you need to empty the S3 bucket because CloudFormation cannot delete a bucket that still contains objects.

Run the following command:

```bash
aws s3 rm s3://ecommerce-aws-backend-frontendbucket-9wu7aid8okd8 --recursive
```

This command deletes all frontend files in the bucket.

After the command finishes, check the bucket again in AWS Console to make sure it is empty.

#### Empty S3 Product Images Bucket

Next, delete all product images from the Product Images Bucket:

```bash
aws s3 rm s3://ecommerce-aws-backend-productimagesbucket-eqn2cddpurel --recursive
```

After deleting the files, check the bucket again:

```text
Amazon S3 → Buckets → Product Images Bucket
```

The bucket should no longer contain any objects.

#### Delete object versions if bucket versioning is enabled

If S3 bucket versioning is enabled, the `aws s3 rm --recursive` command may not delete all object versions.

In this case, open AWS Console:

```text
Amazon S3 → Buckets → select bucket → Versions
```

Then delete all object versions and delete markers.

You can also use the AWS Console option:

```text
Empty bucket
```

and confirm that all object versions should be deleted.

#### Delete backend stack using AWS SAM

Move to the backend or infrastructure folder depending on your project structure:

```bash
cd C:\Users\namda\ecommerce-aws\backend
```

Run the delete command:

```bash
sam delete
```

When SAM asks for confirmation, enter:

```text
y
```

SAM will delete the CloudFormation stack and related resources.

Stack to delete:

```text
ecommerce-aws-backend
```

#### Delete stack using AWS CLI

If you do not use `sam delete`, you can delete the stack using AWS CLI:

```bash
aws cloudformation delete-stack --stack-name ecommerce-aws-backend --region ap-southeast-1
```

Then monitor the deletion status:

```bash
aws cloudformation describe-stacks --stack-name ecommerce-aws-backend --region ap-southeast-1
```

In AWS Console, the stack status will change to:

```text
DELETE_IN_PROGRESS
```

After the deletion is complete, the stack will disappear from the active stacks list.

#### Check CloudFormation Events if delete fails

If stack deletion fails, open:

```text
CloudFormation → Stacks → ecommerce-aws-backend → Events
```

Check which resource caused the error.

Common causes include:

- S3 bucket is not empty.
- S3 bucket still contains object versions.
- CloudFront Distribution has not finished disabling.
- IAM Role is still being used.
- Lambda function still has related event sources or permissions.

If the error is related to S3, empty the bucket and delete the stack again.

#### Check CloudFront Distribution

CloudFront Distribution may take a few minutes to be fully deleted.

Open AWS Console:

```text
CloudFront → Distributions
```

Check the distribution:

```text
E3EAY8XN54SSDX
```

If the distribution still exists, check its status:

```text
Enabled
Disabled
In Progress
```

In some cases, CloudFormation needs to disable the distribution first before it can delete it.

This process may take several minutes.

#### Check API Gateway

After the stack is deleted, check API Gateway:

```text
API Gateway → APIs
```

The e-commerce project API should no longer appear.

If the API still exists, check whether it was created outside CloudFormation.

Workshop API URL:

```text
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod
```

After cleanup, this URL should no longer work.

#### Check Lambda Functions

Open AWS Console:

```text
Lambda → Functions
```

Check for Lambda functions related to the e-commerce project.

Function names usually contain:

```text
ecommerce-aws-backend
```

After deleting the stack, these functions should be removed.

If any function still exists, check whether it was created outside the stack.

#### Check DynamoDB Tables

Open AWS Console:

```text
DynamoDB → Tables
```

Check the following tables:

```text
EcommerceProducts
EcommerceOrders
```

If CloudFormation deletion was successful, these tables should no longer exist.

If the tables still exist, they may have a `DeletionPolicy: Retain` configuration or may have been created manually outside the stack.

#### Check Cognito User Pools

Open AWS Console:

```text
Amazon Cognito → User pools
```

Check the Customer User Pool and Admin User Pool.

User Pools in this workshop:

```text
ap-southeast-1_LHSScZri7
ap-southeast-1_k57hCsM4z
```

After cleanup, User Pools created by the stack should be deleted.

If they still exist and are no longer used, you can delete them manually in Cognito Console.

#### Check EventBridge Schedules

Open AWS Console:

```text
Amazon EventBridge → Scheduler
```

or:

```text
Amazon EventBridge → Rules
```

Check scheduled jobs related to:

```text
payment expiry
refund reconciliation
ecommerce-aws-backend
```

After deleting the stack, these schedules should also be deleted.

If any schedule still exists, delete it manually to avoid unwanted Lambda executions.

#### Check CloudWatch Logs

CloudWatch Log Groups may not be deleted automatically depending on the configuration.

Open AWS Console:

```text
CloudWatch → Logs → Log groups
```

Search for log groups containing:

```text
/aws/lambda/ecommerce-aws-backend
```

If you do not need to keep the logs, you can delete these log groups manually.

#### Check CloudWatch Alarms

Open AWS Console:

```text
CloudWatch → Alarms
```

Check alarms related to the e-commerce project.

After deleting the stack, alarms created by the stack should be removed.

If any alarm still exists, delete it manually to avoid unnecessary alerts.

#### Check SNS Topic

Open AWS Console:

```text
Amazon SNS → Topics
```

Check the topic used to send alert emails.

If the topic still exists and is no longer used, delete it manually.

Then check:

```text
Amazon SNS → Subscriptions
```

Delete email subscriptions if they are no longer needed.

#### Check SES Identity

If you verified an email or domain in Amazon SES, the identity may still exist after stack deletion.

Open AWS Console:

```text
Amazon SES → Configuration → Identities
```

Check the verified email or domain.

If you no longer use SES for this project, you can delete the identity.

Notes:

- Deleting an SES identity means that email or domain can no longer be used to send emails through SES.
- If the email is used by another project, you should not delete it.

#### Check Secrets Manager

If the ZaloPay secret was created in AWS Secrets Manager, check whether it was created by the stack or manually.

Open AWS Console:

```text
AWS Secrets Manager → Secrets
```

Search for secrets related to:

```text
zalopay
ecommerce
```

If the secret is no longer used, you can delete it.

When deleting a secret, AWS may require a waiting period before permanent deletion.

#### Check IAM Roles and Policies

Open AWS Console:

```text
IAM → Roles
```

Search for roles related to:

```text
ecommerce-aws-backend
```

IAM Roles created by CloudFormation are usually deleted with the stack.

If roles or policies still exist, check carefully before deleting them manually.

Do not delete a role if it is being used by another project.

#### Check GitHub Actions OIDC

If you created an OIDC Provider or IAM Role for GitHub Actions CI/CD, check it in IAM.

Open AWS Console:

```text
IAM → Identity providers
```

If the following provider exists:

```text
token.actions.githubusercontent.com
```

only delete it if it is not used by any other repository or project.

Check the IAM Role for GitHub Actions:

```text
IAM → Roles
```

Search for roles related to:

```text
GitHubActions
ecommerce
OIDC
```

If the role is used only for this project and is no longer needed, you can delete it.

#### Check GitHub Secrets

In the GitHub repository, open:

```text
Settings → Secrets and variables → Actions
```

Check the following secrets:

```text
AWS_REGION
AWS_ROLE_TO_ASSUME
ZALOPAY_APP_ID
ZALOPAY_KEY1
ZALOPAY_KEY2
```

If the repository will no longer deploy to AWS, you can delete these secrets.

#### Check website after cleanup

After cleanup is complete, open the CloudFront URL:

```text
https://dfjng6e5jz4mf.cloudfront.net
```

If the resources have been deleted, the website should no longer be accessible or should return an error.

Check the API URL:

```text
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod/products
```

If the API has been deleted, this endpoint will no longer work.

#### Check Billing

After deleting the resources, open AWS Billing:

```text
AWS Billing and Cost Management → Bills
```

Check services that may still generate costs:

- CloudFront.
- S3.
- DynamoDB.
- Lambda.
- API Gateway.
- CloudWatch Logs.
- SNS.
- SES.
- Secrets Manager.
- WAF if used.

Some services may still show charges for the current day because billing data can have a delay.

#### Common cleanup errors

##### CloudFormation cannot delete S3 bucket

Common error:

```text
The bucket you tried to delete is not empty
```

How to fix:

- Empty the bucket.
- Delete object versions if versioning is enabled.
- Run `sam delete` or delete the stack again.

##### CloudFront deletion takes a long time

CloudFront needs time to disable and delete a distribution.

How to fix:

- Wait a few minutes.
- Check the distribution status.
- Do not recreate a stack with conflicting resources while the old distribution is still being deleted.

##### Stack is DELETE_FAILED

If the stack status becomes:

```text
DELETE_FAILED
```

open the **Events** tab to see which resource failed.

After fixing the issue, choose:

```text
Delete stack
```

and try again.

##### Log groups still exist after stack deletion

CloudWatch Log Groups sometimes remain to preserve historical logs.

How to fix:

- Open CloudWatch Logs.
- Select log groups related to the project.
- Delete them manually if you do not need to keep the logs.

##### DynamoDB table still exists

If a DynamoDB table is not deleted, possible causes include:

- `DeletionPolicy: Retain` is configured.
- The table was created manually outside the stack.
- Stack deletion failed before deleting the table.

How to fix:

- Check CloudFormation Events.
- Delete the table manually if you no longer need the data.

#### Cleanup checklist

After finishing cleanup, verify the following checklist:

- S3 Frontend Bucket has been emptied or deleted.
- S3 Product Images Bucket has been emptied or deleted.
- CloudFormation stack `ecommerce-aws-backend` has been deleted.
- API Gateway no longer contains the project API.
- Lambda functions of the project have been deleted.
- DynamoDB tables have been deleted or backed up.
- Cognito User Pools have been deleted if no longer used.
- EventBridge schedules are no longer running.
- CloudWatch Alarms have been deleted.
- SNS Topic and Subscriptions have been deleted if no longer used.
- SES Identity has been deleted if not needed.
- Secrets Manager secrets have been deleted if not used.
- IAM Roles and Policies no longer contain unused project resources.
- GitHub Actions OIDC Role has been deleted if no longer used.
- GitHub Secrets have been deleted if no longer used.
- Billing no longer shows abnormal charges.

#### Result

After completing the cleanup section, the AWS resources for the workshop have been cleaned up.

The results include:

- Frontend static files on S3 have been deleted.
- Product images on S3 have been deleted.
- The main CloudFormation stack has been deleted.
- Related resources such as API Gateway, Lambda, DynamoDB, Cognito, CloudFront, EventBridge, CloudWatch, and SNS have been deleted or checked.
- Remaining resources such as SES, Secrets Manager, IAM Roles, and GitHub Actions OIDC have been checked.
- Billing has been checked to make sure there are no abnormal charges.

After this step, the **Mimi Jewelry E-commerce Platform** workshop has completed the full deployment lifecycle: environment preparation, backend deployment, frontend deployment, authentication, product management, order and payment, monitoring, and cleanup.
