---
title: "Introduction"
date: 2026-05-11
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Introduction to Mimi Jewelry E-commerce Project

- **Mimi Jewelry E-commerce Project** is an online shopping website built using the serverless model on AWS. The project focuses on deploying an e-commerce application with essential features such as product browsing, login, cart management, order placement, online payment, order management, and product image upload.

- The project architecture uses AWS managed services and serverless services to reduce manual server management. Instead of deploying the system on EC2 or managing servers manually, the project uses **Amazon S3, Amazon CloudFront, Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, Amazon EventBridge, Amazon CloudWatch, Amazon SNS, AWS IAM, AWS SAM, and AWS CloudFormation**.

- The project also integrates **ZaloPay Sandbox** to simulate the online payment process. In addition, the system includes background tasks such as checking expired unpaid orders, refund reconciliation, and sending alerts through CloudWatch Alarm combined with SNS.

- The main objective of this project is to build an e-commerce system that can be deployed on AWS, is scalable, easy to operate, includes monitoring, supports CI/CD, and follows a production-like architecture.

#### Workshop Overview

In this workshop, you will deploy an **AWS Serverless E-commerce Platform** that includes frontend, backend, database, authentication, image storage, payment, monitoring, and CI/CD.

The system is divided into the following main components:

- **Frontend Layer** is used to display the shopping website interface for customers and admins. The frontend is built into static files, stored in an **Amazon S3 Frontend Bucket**, and delivered to users through **Amazon CloudFront**.

- **Security & Delivery Layer** uses **AWS WAF** to protect requests before they reach CloudFront. CloudFront helps improve access speed, cache static content, and deliver the website to users with low latency.

- **Authentication Layer** uses **Amazon Cognito** to manage user registration, login, and authentication. The project can separate customer and admin user pools to manage access permissions more clearly.

- **API Layer** uses **Amazon API Gateway** to receive requests from the frontend. API Gateway acts as the main access point between the frontend and backend.

- **Compute Layer** uses **AWS Lambda** to process backend logic such as product management, cart management, order management, payment processing, image upload, ZaloPay callback handling, and admin-related operations.

- **Database Layer** uses **Amazon DynamoDB** to store product, order, and refund data. DynamoDB is suitable for serverless architecture because it supports automatic scaling, low latency, and does not require database server management.

- **Storage Layer** uses **Amazon S3 Product Images Bucket** to store product images. The backend generates presigned URLs so that admins can upload images to S3 securely.

- **Payment Layer** integrates **ZaloPay Sandbox** to simulate the online payment process. When a customer places an order, the backend creates a payment request, redirects the customer to ZaloPay, and handles the payment result through a callback.

- **Async & Scheduled Processing Layer** uses **Amazon EventBridge** to run scheduled tasks such as checking expired unpaid orders and reconciling refunds.

- **Monitoring & Alert Layer** uses **Amazon CloudWatch** to collect logs, metrics, and create alarms. When the system has errors or abnormal metrics, **Amazon SNS** sends alert emails to the system operator.

- **CI/CD Layer** uses **GitHub Actions** together with **AWS IAM OIDC Role**, **AWS SAM**, and **AWS CloudFormation** to automatically deploy the backend, update the frontend to S3, and create CloudFront invalidation after each deployment.

#### Architecture Overview

The main request flow of the system can be described as follows:

```text
User / Admin
   ↓
AWS WAF
   ↓
Amazon CloudFront
   ↓
Amazon S3 Frontend Bucket
   ↓
Amazon API Gateway
   ↓
AWS Lambda
   ↓
Amazon DynamoDB / Amazon S3 Product Images Bucket
```

Payment and background processing flow:

```text
User Checkout
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

Scheduled jobs flow:

```text
Amazon EventBridge
   ↓
PaymentExpirySchedulerFunction / RefundReconciliationFunction
   ↓
Amazon DynamoDB
   ↓
Amazon CloudWatch Logs
```

Monitoring and alert flow:

```text
API Gateway / Lambda / EventBridge
   ↓
Amazon CloudWatch Logs & Metrics
   ↓
CloudWatch Alarm
   ↓
Amazon SNS
   ↓
Alert Email
```

#### Main Features in This Workshop

After completing this workshop, the system will include the following main features:

- Customers can view the product list.
- Customers can view product details.
- Customers can register and log in using Amazon Cognito.
- Customers can add products to the cart.
- Customers can create orders.
- Customers can pay through ZaloPay Sandbox.
- Admins can log in to the admin dashboard.
- Admins can create, update, delete, and manage products.
- Admins can upload product images to Amazon S3.
- The system stores product and order data in DynamoDB.
- The system has scheduled jobs to process expired orders and refunds.
- The system has CloudWatch Logs for debugging Lambda and API Gateway.
- The system has CloudWatch Alarm and SNS to send alert emails.
- The system has CI/CD with GitHub Actions for automatic deployment.

#### Architecture Diagram

<img src="/images/2-Proposal/aws-ecommerce-architecture.jpg"
     alt="AWS Serverless E-commerce Architecture"
     style="max-width:100%;height:auto;">

#### Expected Outcome

After this workshop, you will understand how to build a serverless e-commerce website on AWS in a production-like approach. You will also understand how AWS services work together in a real system, from frontend, backend, authentication, database, payment, monitoring, to CI/CD.

This project does not use EC2, ECS, RDS, or ALB in the main architecture. Instead, the system prioritizes serverless services and managed services to reduce operational cost, improve scalability, and fit the requirements of a modern e-commerce project.
