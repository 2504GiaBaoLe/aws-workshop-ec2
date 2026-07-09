---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

{{% notice info %}}
This project proposes the development of the **Mimi Jewelry E-commerce Platform** based on an **AWS Serverless** architecture to deliver a modern, scalable, secure, and cost-optimized e-commerce platform.
{{% /notice %}}

In this section, the team presents the overall solution, project objectives, architecture, and implementation plan for the Mimi Jewelry E-commerce Platform.

# Mimi Jewelry E-commerce Platform
## AWS Serverless E-commerce Solution

### 1. Executive Summary

The **Mimi Jewelry E-commerce Platform** is an e-commerce solution built on an **AWS Serverless** architecture to provide a modern, secure, and highly scalable online shopping system. The platform supports product management, order processing, online payment, and an administrative portal for system administrators.

The solution leverages Amazon Route 53, AWS WAF, Amazon CloudFront, Amazon S3, Amazon Cognito, Amazon API Gateway, AWS Lambda, and Amazon DynamoDB to build a cloud-native application. Deploying the system using a serverless architecture significantly reduces operational costs while enabling automatic scaling based on application traffic.

In addition, the platform integrates ZaloPay for online payment processing, GitHub Actions for CI/CD automation, and AWS CloudFormation to provision and manage the entire infrastructure using the Infrastructure as Code (IaC) approach.

### Project Objectives

- Develop an e-commerce website based on an AWS Serverless architecture.
- Reduce operational costs using the Pay-as-you-go pricing model.
- Automate application deployment through GitHub Actions and AWS CloudFormation.
- Integrate user authentication and secure online payment capabilities.
- Implement AWS security and monitoring services to enhance system reliability and protection.


### 2. Problem Statement

*Current Challenges*

Traditional e-commerce systems are typically deployed on physical or virtual servers, requiring developers to manage the entire infrastructure, including application servers, databases, storage systems, load balancers, security, monitoring, and data backup. As the number of users increases, scaling system resources often requires additional configuration or infrastructure upgrades, resulting in increased operational complexity, time, and cost.

For individual developers or small teams, investing in and maintaining a traditional infrastructure not only increases operational expenses but also requires extensive expertise in system administration. Furthermore, integrating features such as user authentication, online payment processing, product image storage, system monitoring, and automated deployment often involves multiple technologies, making the development process significantly more complex.

In addition, e-commerce platforms must satisfy strict requirements for security, availability, and scalability. Without an appropriate architecture, the system may experience performance degradation during traffic spikes, security vulnerabilities, or high operational costs due to continuously running infrastructure resources.

*Proposed Solution*

To address these challenges, the project adopts an **AWS Serverless** architecture to minimize infrastructure management while leveraging the automatic scalability provided by AWS services.

Users access the website through a domain managed by **Amazon Route 53**. All incoming requests are first routed to **Amazon CloudFront**, which delivers static content globally with low latency. Before reaching CloudFront, requests are inspected and filtered by **AWS WAF**, providing an additional security layer to protect the application from malicious traffic.

The frontend is implemented as a **Static Website** hosted on **Amazon S3** and distributed globally through CloudFront. User and administrator authentication is handled by **Amazon Cognito**, which manages user registration, sign-in, and JWT token issuance to secure API access.

All frontend requests are sent to **Amazon API Gateway**, which serves as the communication layer between the frontend and backend. API Gateway invokes **AWS Lambda Functions** to execute all business logic, including product management, order processing, image upload URL generation, payment processing, account management, and administrative operations.

Product and order information is stored in **Amazon DynamoDB**, providing flexible scalability and high-performance read/write operations. Product images are stored in an **Amazon S3 Product Image Bucket**, where the system utilizes **Pre-signed URLs** to allow administrators to upload images directly to Amazon S3 without routing the file data through the backend server.

The platform integrates **ZaloPay** for online payment processing. When a customer initiates a payment, an AWS Lambda function generates a payment request and sends it to ZaloPay. After the transaction is completed, ZaloPay returns a callback to the system, allowing the order status stored in Amazon DynamoDB to be updated accordingly.

For background processing, **Amazon EventBridge** schedules AWS Lambda functions to periodically check expired unpaid orders and perform refund reconciliation when necessary. All application logs and operational metrics are collected by **Amazon CloudWatch**. Whenever errors are detected or predefined alarm thresholds are exceeded, CloudWatch sends notifications through **Amazon SNS** to the administrator's email.

Deployment is fully automated using **GitHub Actions**, which authenticates with AWS through an **IAM OIDC Role**, eliminating the need to store AWS Access Keys in the repository. The infrastructure is provisioned using **AWS CloudFormation**, ensuring consistent deployments and enabling easy redeployment across different environments.

*Benefits and Return on Investment (ROI)*

The proposed solution significantly reduces server administration efforts while leveraging AWS's automatic scaling capabilities to handle traffic fluctuations without manual intervention. By utilizing serverless services such as **Amazon S3**, **Amazon CloudFront**, **Amazon API Gateway**, **AWS Lambda**, and **Amazon DynamoDB**, the platform optimizes infrastructure costs through the **Pay-as-you-go** pricing model, charging only for actual resource consumption.

Beyond its technical advantages, the project provides an opportunity to integrate a wide range of AWS services into a complete production-like system, including storage, networking, authentication, data processing, monitoring, security, and automated deployment. This practical experience strengthens understanding of cloud-native architecture and modern application deployment on AWS.

During the learning and demonstration phase, the estimated infrastructure cost is expected to remain between **USD 0–10 per month**, assuming low traffic volume and proper resource cleanup after use. When deployed in a production environment, the platform can scale to support a larger user base without requiring architectural changes, thereby reducing initial investment costs while improving long-term operational efficiency.

### 3. Solution Architecture

The system is designed using an **AWS Serverless** architecture. Users access the website through **Amazon Route 53**, which routes requests to **Amazon CloudFront**. Before requests reach CloudFront, **AWS WAF** inspects incoming traffic and protects the application against malicious requests.

CloudFront distributes the frontend from the **Amazon S3 Frontend Bucket**. The frontend is a static website built from the application source code and hosted in Amazon S3. When users visit the website, CloudFront serves static assets such as HTML, CSS, and JavaScript files.

The platform uses **Amazon Cognito** for user authentication. Cognito manages the sign-in process and returns authentication tokens. These tokens are included in frontend requests sent to **Amazon API Gateway**. API Gateway functions as the API layer, receiving requests and invoking **AWS Lambda** functions to process business logic.

The **Compute Layer** is powered by **AWS Lambda**. The primary Lambda functions include **EcommerceBackendFunction**, **RefundReconciliationFunction**, and **PaymentExpirySchedulerFunction**. EcommerceBackendFunction handles core business operations such as product management, order management, checkout, image uploads, payment processing, and administrative functions. RefundReconciliationFunction performs refund reconciliation, while PaymentExpirySchedulerFunction checks overdue unpaid orders.

The **Data Layer** uses **Amazon DynamoDB** for data storage. The primary tables include **Products** and **Orders**. The Products table stores product information, pricing, descriptions, images, inventory levels, and product status. The Orders table stores customer information, order details, payment status, and order processing status.

The **Amazon S3 Product Image Bucket** is used to store product images. When administrators upload product images, the backend processes the upload request and stores the images in this bucket. These images are subsequently displayed on the website.

The **Integration Layer** consists of **Amazon EventBridge** and **ZaloPay**. EventBridge triggers scheduled AWS Lambda functions for background processing, while ZaloPay serves as the third-party online payment provider. Lambda functions send payment requests to ZaloPay, which then returns payment results to the system.

The **Monitoring Layer** includes **Amazon CloudWatch**, **Amazon SNS**, **Amazon Cognito AdminPool**, and **Alert Email**. CloudWatch collects logs and metrics from API Gateway, Lambda, and related AWS services. When errors occur or alarm thresholds are exceeded, CloudWatch sends alerts to SNS, which then forwards notification emails to administrators. Amazon Cognito AdminPool authenticates administrators within the management portal and helps enforce administrative access control.

The **CI/CD Layer** consists of **GitHub**, **GitHub Actions**, **IAM OIDC Role**, and **AWS CloudFormation**. Developers push source code to GitHub, after which GitHub Actions automatically builds and deploys the application to AWS using IAM OIDC Role authentication. AWS CloudFormation provisions infrastructure resources including Lambda Functions, API Gateway, Amazon S3 Frontend Bucket, CloudFront Invalidation, and other related AWS resources.

<img src="/images/2-Proposal/aws-ecommerce-architecture.jpg"
     alt="AWS Serverless E-commerce Architecture"
     style="max-width:100%;height:auto;">

AWS Services Used

- **Amazon Route 53:** Manages the website domain and DNS records.
- **AWS WAF:** Inspects incoming requests and protects the application before traffic reaches CloudFront.
- **Amazon CloudFront:** Delivers the frontend and static content through a global CDN.
- **Amazon S3 Frontend Bucket:** Stores the frontend static website files.
- **Amazon S3 Product Image Bucket:** Stores product images.
- **Amazon Cognito:** Manages user and administrator authentication.
- **Amazon API Gateway:** Receives frontend requests and forwards them to AWS Lambda.
- **AWS Lambda:** Executes backend business logic, including product management, order processing, payment processing, image uploads, and background tasks.
- **Amazon DynamoDB:** Stores product and order data.
- **Amazon EventBridge:** Triggers scheduled AWS Lambda background tasks.
- **Amazon CloudWatch:** Collects logs and metrics while supporting alarm creation.
- **Amazon SNS:** Sends operational alert notifications via email.
- **AWS IAM:** Manages access permissions across AWS services.
- **AWS CloudFormation:** Provisions and manages infrastructure using Infrastructure as Code templates.

Third-Party Services

- **ZaloPay:** Third-party payment gateway for processing online transactions.
- **GitHub Actions:** Automates the application build and deployment pipeline.

Component Design

- **User Layer:** Users access the website through the domain.
- **DNS Layer:** Amazon Route 53 routes requests to Amazon CloudFront.
- **Security Layer:** AWS WAF inspects requests before they reach CloudFront.
- **Frontend Layer:** CloudFront distributes frontend content from the Amazon S3 Frontend Bucket.
- **Authentication Layer:** Amazon Cognito manages user authentication and JWT tokens.
- **API Layer:** Amazon API Gateway receives requests and invokes AWS Lambda functions.
- **Compute Layer:** AWS Lambda executes all backend business logic.
- **Data Layer:** Amazon DynamoDB stores the Products and Orders tables.
- **Storage Layer:** Amazon S3 Product Image Bucket stores product images.
- **Integration Layer:** Amazon EventBridge manages scheduled tasks, while ZaloPay processes online payments.
- **Monitoring Layer:** Amazon CloudWatch, Amazon SNS, and Alert Email monitor system health and operational issues.
- **CI/CD Layer:** GitHub Actions, IAM OIDC Role, and AWS CloudFormation automate application deployment.


### 4. Technical Implementation

*Implementation Phases*

1. **AWS Service Research:** Study Amazon S3, Amazon CloudFront, Amazon Route 53, AWS WAF, Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, Amazon EventBridge, Amazon CloudWatch, Amazon SNS, AWS IAM, and AWS CloudFormation.
2. **Architecture Design:** Design the serverless architecture for the e-commerce platform.
3. **Frontend Development:** Develop the storefront, product pages, checkout page, and administrative portal.
4. **Backend Development:** Build the backend using Node.js/TypeScript running on AWS Lambda.
5. **API Layer Configuration:** Configure Amazon API Gateway to receive requests from the frontend.
6. **Database Integration:** Use Amazon DynamoDB to store the **Products** and **Orders** tables.
7. **Authentication Integration:** Configure Amazon Cognito for user and administrator authentication.
8. **Frontend Deployment:** Upload the static frontend to Amazon S3 and distribute it through Amazon CloudFront.
9. **Security Enhancement:** Configure AWS WAF to inspect incoming requests before they reach CloudFront.
10. **Image Upload:** Use the Amazon S3 Product Image Bucket to store product images.
11. **Payment Integration:** Integrate ZaloPay to process payment requests and callbacks.
12. **Background Processing:** Use Amazon EventBridge to trigger scheduled AWS Lambda functions.
13. **Monitoring and Alerts:** Configure Amazon CloudWatch, Amazon SNS, and Alert Email notifications.
14. **CI/CD:** Implement automated deployment using GitHub Actions, IAM OIDC Role, and AWS CloudFormation.

*Technical Requirements*

- *Frontend:* Static website hosted on Amazon S3 and distributed through Amazon CloudFront.
- *Backend:* Node.js/TypeScript running on AWS Lambda with Amazon API Gateway.
- *Database:* Amazon DynamoDB with **Products** and **Orders** tables.
- *Authentication:* Amazon Cognito for user and administrator authentication.
- *Security:* AWS WAF, IAM Policies, private Amazon S3 buckets, and Amazon CloudFront.
- *Storage:* Amazon S3 Frontend Bucket and Amazon S3 Product Image Bucket.
- *Payment:* ZaloPay third-party payment service.
- *Monitoring:* Amazon CloudWatch Logs, Metrics, Alarms, and Amazon SNS.
- *Deployment:* GitHub Actions, IAM OIDC Role, and AWS CloudFormation.

### 5. Project Roadmap & Implementation Milestones

- **Weeks 1–3:** Study core AWS and serverless services.
  - Learn AWS Console, AWS IAM, Amazon S3, Amazon CloudFront, Amazon Route 53, AWS WAF, Amazon DynamoDB, Amazon Cognito, AWS Lambda, Amazon API Gateway, Amazon EventBridge, Amazon CloudWatch, and Amazon SNS.

- **Week 4:** Requirements analysis and architecture design.
  - Define the system requirements.
  - Design the serverless architecture diagram.
  - Identify the AWS services required for the solution.

- **Week 5:** Frontend development.
  - Home page.
  - Product listing page.
  - Product details page.
  - Shopping cart.
  - Checkout page.
  - Administrative dashboard.

- **Week 6:** Backend development.
  - Develop the backend AWS Lambda functions.
  - Configure Amazon API Gateway.
  - Build APIs for products, orders, and administration.

- **Week 7:** Amazon DynamoDB integration.
  - Design the **Products** and **Orders** tables.
  - Connect AWS Lambda functions to Amazon DynamoDB.

- **Week 8:** Amazon Cognito integration.
  - Configure user and administrator authentication.
  - Implement JWT token validation and API authorization.

- **Week 9:** Frontend deployment.
  - Build the frontend application.
  - Upload the build artifacts to Amazon S3.
  - Configure Amazon CloudFront distribution.

- **Week 10:** Image upload and payment integration.
  - Configure the Amazon S3 Product Image Bucket.
  - Implement image upload functionality.
  - Integrate ZaloPay payment requests and callback handling.

- **Week 11:** Scheduled jobs and monitoring.
  - Configure Amazon EventBridge.
  - Implement **PaymentExpirySchedulerFunction**.
  - Implement **RefundReconciliationFunction**.
  - Configure Amazon CloudWatch and Amazon SNS.

- **Week 12:** Finalize CI/CD, security, and documentation.
  - Configure GitHub Actions.
  - Configure IAM OIDC Role.
  - Deploy infrastructure using AWS CloudFormation.
  - Complete the Proposal, Workshop documentation, Worklog, and resource cleanup.

  ### 6. Budget Estimation

The project cost depends on the number of requests, storage capacity, CloudFront traffic, CloudWatch log volume, and whether a custom domain is used.

Estimated infrastructure costs for the learning/demo phase

- **Amazon S3:** Stores the frontend application and product images.
- **Amazon CloudFront:** Delivers website content and product images through a global CDN.
- **AWS WAF:** Incurs charges when Web ACLs and security rules are enabled.
- **AWS Lambda:** Charged based on the number of invocations and execution duration.
- **Amazon API Gateway:** Charged according to the number of API requests.
- **Amazon DynamoDB:** Charged based on read/write requests or provisioned storage capacity.
- **Amazon CloudWatch:** Costs are incurred for logs, metrics, and alarms.
- **Amazon SNS:** Low-cost service for email alert notifications.
- **Amazon Route 53:** Charges apply for hosted zones and domain registration when using a custom domain.
- **AWS CloudFormation:** No additional charge for stack management; users only pay for the AWS resources provisioned.

Estimated Total Cost

- **Learning/Demo Phase:** Approximately **USD 0–10 per month**, assuming low traffic volume and proper resource cleanup.
- **Custom Domain:** Annual registration fees apply if the domain is registered through Amazon Route 53.
- **ZaloPay:** The project uses the sandbox/testing environment, so no actual payment transactions are incurred.

### 7. Risk Assessment

*Risk Matrix*

- Incorrect IAM configuration or overly permissive access policies: **High impact**, **Medium probability**.
- Misconfigured AWS WAF rules blocking legitimate requests: **Medium impact**, **Low probability**.
- Incorrect Amazon S3 bucket policy or Amazon CloudFront configuration: **Medium impact**, **Medium probability**.
- Amazon Cognito or JWT authentication failures: **Medium impact**, **Medium probability**.
- ZaloPay payment callback failures: **High impact**, **Medium probability**.
- Excessive Amazon CloudWatch Logs resulting in increased operational costs: **Medium impact**, **Low probability**.
- GitHub Actions deployment failures caused by IAM OIDC Role or authentication issues: **Medium impact**, **Medium probability**.
- Amazon Route 53 domain or HTTPS configuration not completed: **Low to Medium impact**, **Medium probability**.

*Mitigation Strategies*

- **IAM:** Apply the principle of least privilege for every IAM role.
- **AWS WAF:** Begin with basic security rules and monitor logs before enforcing stricter blocking rules.
- **Amazon S3 / Amazon CloudFront:** Use private Amazon S3 buckets and serve content exclusively through Amazon CloudFront.
- **Authentication:** Validate JWT tokens and clearly separate user and administrator authorization.
- **Payment:** Verify callback URLs, secret keys, and order status before updating payment information.
- **Cost Control:** Configure billing alerts and regularly clean up unused AWS resources.
- **CI/CD:** Restrict IAM OIDC Role permissions to the designated GitHub repository and deployment branches.
- **Domain:** If Amazon Route 53 configuration is incomplete, use the Amazon CloudFront distribution URL for demonstrations.

*Contingency Plan*

- If the custom domain is not ready, use the Amazon CloudFront distribution URL.
- If AWS WAF blocks legitimate requests, temporarily switch the rule to **Count** mode or disable the rule.
- If GitHub Actions deployment fails, perform a manual deployment using **AWS SAM CLI** and **AWS CLI**.
- If ZaloPay callback processing fails, review Amazon CloudWatch Logs and manually update the order status in Amazon DynamoDB when necessary.
- If Amazon CloudFront serves outdated content, perform a **CloudFront Invalidation** to refresh the cache.

### 8. Expected Outcomes

**Technical Improvements:**  
The project delivers a complete serverless e-commerce platform consisting of all essential components, including the frontend, backend APIs, authentication, database, image upload functionality, payment integration, scheduled background jobs, monitoring, security layers, and a fully automated CI/CD pipeline.

**Learning Value:**  
Through this project, the development team gains practical experience in integrating multiple AWS services into a real-world cloud-native application, including **Amazon Route 53, AWS WAF, Amazon CloudFront, Amazon S3, Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, Amazon EventBridge, Amazon CloudWatch, Amazon SNS, AWS IAM, and AWS CloudFormation**.

**Long-Term Value:**  
The platform can be further expanded into a production-ready e-commerce solution by incorporating a custom domain, HTTPS certificates using **AWS Certificate Manager (ACM)**, revenue analytics dashboards, shipping provider integration, additional payment gateways, recommendation systems, and dedicated staging and production environments.