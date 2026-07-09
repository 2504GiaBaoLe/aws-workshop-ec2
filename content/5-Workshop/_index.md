---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

#### Introduction to Mimi Jewelry E-commerce Project

- **Mimi Jewelry E-commerce Project** is an online shopping website built using the serverless model on AWS. The project focuses on deploying an e-commerce application with essential features such as product browsing, login, cart management, order placement, online payment, order management, and product image upload.

- The project architecture uses AWS managed services and serverless services to reduce manual server management. Instead of deploying the system on EC2 or managing servers manually, the project uses **Amazon S3, Amazon CloudFront, Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, Amazon EventBridge, Amazon CloudWatch, Amazon SNS, AWS IAM, AWS SAM, and AWS CloudFormation**.

- The project also integrates **ZaloPay Sandbox** to simulate the online payment process. In addition, the system includes background tasks such as checking expired unpaid orders, refund reconciliation, and sending alerts through CloudWatch Alarm combined with SNS.

- The main objective of this project is to build an e-commerce system that can be deployed on AWS, is scalable, easy to operate, includes monitoring, supports CI/CD, and follows a production-like architecture.

#### Content

1. [Workshop overview](5.1-Workshop-overview)
2. [Prerequiste](5.2-Prerequiste/)
3. [Backend deployment](5.3-Backend-deployment/)
4. [Frontend deployment](5.4-Fronten-deployment/)
5. [Authentication](5.5-Authentication/)
6. [Product management](5.6-Product-management/)
7. [Clean up](5.7-Cleanup/)
