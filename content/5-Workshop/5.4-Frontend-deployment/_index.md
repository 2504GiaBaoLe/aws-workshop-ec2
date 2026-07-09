---
title: "Frontend Deployment"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Objective

In this section, we will deploy the frontend of the **Mimi Jewelry E-commerce Platform** project.

The frontend of this project is built with **Next.js** and deployed as a static website. After the build process, the static files will be uploaded to the **Amazon S3 Frontend Bucket** and distributed to the Internet through **Amazon CloudFront**.

The main contents of this section include:

- Configure environment variables for the frontend.
- Build the Next.js frontend.
- Upload static files to Amazon S3.
- Create a CloudFront invalidation.
- Check the website after deployment.
- Test the main customer and admin features.

#### Frontend architecture

The frontend is deployed using a static hosting model combined with a CDN.

Frontend access flow:

```text
User / Admin
   ↓
Amazon CloudFront
   ↓
Amazon S3 Frontend Bucket
   ↓
Next.js static files
```

When users access the website, the request goes through CloudFront. CloudFront retrieves content from the S3 Frontend Bucket and returns it to the user.

When the frontend needs to get product data, log in, place orders, or manage admin features, it calls API Gateway.

Frontend-to-backend flow:

```text
Frontend Website
   ↓
Amazon API Gateway
   ↓
AWS Lambda
   ↓
Amazon DynamoDB
```

#### Move to the frontend folder

Open the terminal at the project directory:

```text
C:\Users\namda\ecommerce-aws
```

Then move to the frontend folder:

```bash
cd frontend
```

Check the main files in the frontend folder:

```text
frontend/
├── src/
├── public/
├── package.json
├── next.config.ts
├── tsconfig.json
└── .env.local
```

Where:

- `src/` contains the Next.js UI source code.
- `public/` contains images and static assets.
- `package.json` contains scripts and dependencies.
- `next.config.ts` contains Next.js configuration.
- `tsconfig.json` contains TypeScript configuration.
- `.env.local` contains environment variables used by the frontend.

#### Install frontend dependencies

Inside the `frontend` folder, run:

```bash
npm install
```

This command installs the required libraries to run and build the frontend.

After installation, check that the following folder has been created:

```text
frontend/node_modules/
```

<img src="/images/5-Workshop/5.4-Frontend-deployment/frontend-npm.png" alt="Install Frontend Dependencies" style="max-width: 100%; height: auto;">

#### Configure frontend environment variables

The frontend needs backend information such as the API Gateway URL, Cognito User Pool ID, and Cognito App Client ID.

Create or open the following file:

```text
frontend/.env.local
```

Add the following content:

```env
NEXT_PUBLIC_API_BASE_URL=https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod

NEXT_PUBLIC_AWS_REGION=ap-southeast-1

NEXT_PUBLIC_CUSTOMER_USER_POOL_ID=ap-southeast-1_LHSScZri7
NEXT_PUBLIC_CUSTOMER_USER_POOL_CLIENT_ID=1uijbf07brda1p130293v3prfv

NEXT_PUBLIC_ADMIN_USER_POOL_ID=ap-southeast-1_k57hCsM4z
NEXT_PUBLIC_ADMIN_USER_POOL_CLIENT_ID=1qc3ttu4q3e95pk7mlqsivr66e

NEXT_PUBLIC_PRODUCT_IMAGES_BASE_URL=https://dfjng6e5jz4mf.cloudfront.net/uploads
```

Notes:

- With Next.js, environment variables used in the browser must start with `NEXT_PUBLIC_`.
- Do not put real secrets in the `.env.local` file.
- These values are taken from the **Outputs** tab of the CloudFormation stack.
- If API Gateway or Cognito changes, update the `.env.local` file and rebuild the frontend.

<img src="/images/5-Workshop/5.4-Frontend-deployment/tao-env.png" alt="Frontend Environment Variables" style="max-width: 100%; height: auto;">

#### Check package.json

Open the following file:

```text
frontend/package.json
```

Check that the `scripts` section contains the main commands as follows:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

In this workshop, we will use:

```bash
npm run build
```

to build the production frontend.

#### Run the frontend locally for testing

Before deployment, you should run the frontend locally to test the UI and API connection.

Run the command:

```bash
npm run dev
```

Expected result:

```text
Local: http://localhost:3000
```

Open the browser:

```text
http://localhost:3000
```

Check the main pages:

- Home page.
- Product list page.
- Product detail page.
- Cart page.
- Checkout page.
- Customer login page.
- Customer registration page.
- Order history page.
- Admin login page.
- Admin dashboard.
- Product management page.
- Order management page.
- Refund management page.

<img src="/images/5-Workshop/5.4-Frontend-deployment/frontend-local.png" alt="Frontend Local Website" style="max-width: 100%; height: auto;">

#### Build the production frontend

After local testing is successful, build the production frontend:

```bash
npm run build
```

Expected result:

```text
Compiled successfully
```

If the project is configured for static export, Next.js will create an output folder after the build process. This folder will be uploaded to S3.

The output folder is usually:

```text
frontend/out/
```

<img src="/images/5-Workshop/5.4-Frontend-deployment/frontend-build.png" alt="Frontend Build Success" style="max-width: 100%; height: auto;">

#### Handle common build errors

##### TypeScript error in the .next folder

If you encounter an error related to:

```text
.next/dev/types/validator.ts
```

it may be caused by a broken Next.js type cache.

How to fix:

```bash
rm -rf .next
npm run build
```

On Windows PowerShell, you can use:

```powershell
Remove-Item -Recurse -Force .next
npm run build
```

##### Missing environment variables

If the frontend calls the wrong API or does not display data, check the following file again:

```text
frontend/.env.local
```

Make sure the environment variables have the following prefix:

```text
NEXT_PUBLIC_
```

##### API CORS error

If the browser shows a CORS error when the frontend calls API Gateway, check the CORS configuration in API Gateway or Lambda response headers.

Common required headers include:

```text
Access-Control-Allow-Origin
Access-Control-Allow-Headers
Access-Control-Allow-Methods
```

#### Check the output folder

After the build succeeds, check the following folder:

```text
frontend/out/
```

This folder contains static files such as:

```text
out/
├── index.html
├── products/
├── account/
├── admin/
├── _next/
└── 404.html
```

These files will be uploaded to the S3 Frontend Bucket.

#### Upload frontend to Amazon S3

After the build is complete, upload all contents inside the `out` folder to the S3 Frontend Bucket.

Upload command:

```bash
aws s3 sync out/ s3://ecommerce-aws-backend-frontendbucket-9wu7aid8okd8 --delete
```

Explanation:

- `out/` is the folder containing static files after the build.
- `s3://ecommerce-aws-backend-frontendbucket-9wu7aid8okd8` is the Frontend Bucket.
- `--delete` removes old files from S3 if they no longer exist locally.

<img src="/images/5-Workshop/5.4-Frontend-deployment/s3-sync.png" alt="Upload Frontend to S3" style="max-width: 100%; height: auto;">

#### Check files in the S3 bucket

Open AWS Console:

```text
Amazon S3 → Buckets
```

Select the Frontend Bucket:

```text
ecommerce-aws-backend-frontendbucket-9wu7aid8okd8
```

Check whether the frontend files have been uploaded, for example:

```text
index.html
404.html
_next/
products/
admin/
account/
```

If these files appear in the bucket, the frontend upload process has completed successfully.

<img src="images/5-Workshop/5.4-Frontend-deployment/s3-frontend-files.png" alt="S3 Frontend Files" style="max-width: 100%; height: auto;">

#### Create CloudFront invalidation

After uploading the frontend to S3, create a CloudFront invalidation so CloudFront can fetch the latest version.

Run the command:

```bash
aws cloudfront create-invalidation --distribution-id E3EAY8XN54SSDX --paths "/*"
```

Explanation:

- `E3EAY8XN54SSDX` is the CloudFront Distribution ID.
- `--paths "/*"` asks CloudFront to refresh the entire cache.

The result will look like this:

```json
{
  "Location": "https://cloudfront.amazonaws.com/...",
  "Invalidation": {
    "Id": "IXXXXXXXXXXXX",
    "Status": "InProgress"
  }
}
```

<img src="/images/5-Workshop/5.4-Frontend-deployment/cloudfront-invalidation.png" alt="CloudFront Invalidation" style="max-width: 100%; height: auto;">

#### Check CloudFront invalidation

Open AWS Console:

```text
CloudFront → Distributions
```

Select the distribution:

```text
E3EAY8XN54SSDX
```

Open the tab:

```text
Invalidations
```

Expected status:

```text
Completed
```

If the status is still:

```text
InProgress
```

wait a few more minutes.

<img src="/images/5-Workshop/5.4-Frontend-deployment/done.png" alt="CloudFront Invalidation Complete" style="max-width: 100%; height: auto;">

#### Open the website using the CloudFront URL

After the invalidation is completed, open the website using the CloudFront URL:

```text
https://dfjng6e5jz4mf.cloudfront.net
```

If the website displays the correct UI, the frontend has been deployed successfully.

<img src="/images/5-Workshop/5.4-Frontend-deployment/cloudfront-website.png" alt="CloudFront Website" style="max-width: 100%; height: auto;">

#### Check customer pages

Access and check the customer pages:

```text
https://dfjng6e5jz4mf.cloudfront.net/
https://dfjng6e5jz4mf.cloudfront.net/products
https://dfjng6e5jz4mf.cloudfront.net/product
https://dfjng6e5jz4mf.cloudfront.net/cart
https://dfjng6e5jz4mf.cloudfront.net/checkout
https://dfjng6e5jz4mf.cloudfront.net/account/login
https://dfjng6e5jz4mf.cloudfront.net/account/register
https://dfjng6e5jz4mf.cloudfront.net/account/orders
```

Features to check:

- The home page is displayed correctly.
- The product list is loaded from the API.
- The product detail page works.
- Products can be added to the cart.
- The checkout page is displayed correctly.
- Customers can register.
- Customers can log in.
- Customers can view order history.

#### Check admin pages

Access and check the admin pages:

```text
https://dfjng6e5jz4mf.cloudfront.net/admin/login
https://dfjng6e5jz4mf.cloudfront.net/admin
https://dfjng6e5jz4mf.cloudfront.net/admin/products
https://dfjng6e5jz4mf.cloudfront.net/admin/orders
https://dfjng6e5jz4mf.cloudfront.net/admin/refunds
```

Features to check:

- Admin can log in.
- Admin dashboard is displayed correctly.
- Admin can view the product list.
- Admin can create a new product.
- Admin can upload product images.
- Admin can view orders.
- Admin can manage refunds.

<img src="/images/5-Workshop/5.4-Frontend-deployment/admin-page.png" alt="Admin Page" style="max-width: 100%; height: auto;">

#### Check frontend requests to API Gateway

Open Developer Tools in the browser:

```text
F12 → Network
```

Then reload the website and check API requests.

Common API requests include:

```text
/products
/product
/cart
/checkout
/account/orders
/admin/products
/admin/orders
```

If the status code is:

```text
200
```

it means the frontend successfully called the API.

If you see:

```text
401 Unauthorized
```

check the login token or Cognito configuration.

If you see:

```text
403 Forbidden
```

check permissions or the authorizer.

If you see:

```text
500 Internal Server Error
```

open CloudWatch Logs of the Lambda function to debug.

#### Common errors when deploying frontend

##### Website still shows the old version

The common cause is that CloudFront is still caching the old version.

How to fix:

```bash
aws cloudfront create-invalidation --distribution-id E3EAY8XN54SSDX --paths "/*"
```

##### Child routes fail when refreshing

For example, refreshing this page:

```text
/admin/products
```

returns a 403 or 404 error.

The cause is that S3 does not understand Next.js static export routes automatically.

How to fix:

- Check the CloudFront Function or error response rewrite.
- Make sure CloudFront rewrites routes to `index.html` or the correct HTML file.
- Check the Next.js static export configuration.

##### Frontend calls the wrong API URL

If the frontend calls the wrong backend URL, check:

```text
frontend/.env.local
```

After editing `.env.local`, rebuild the frontend:

```bash
npm run build
```

Then upload it to S3 again.

##### Product images are not displayed

Check the following possible causes:

- The product image URL in DynamoDB is correct.
- The S3 Product Images Bucket contains the image file.
- CloudFront distributes the correct `/uploads` path.
- S3 CORS is configured correctly.

##### Admin is logged out or session expired

This may happen because the Cognito token has expired or the User Pool Client configuration is incorrect.

How to fix:

- Log in again as admin.
- Check the Admin User Pool ID.
- Check the Admin User Pool Client ID.
- Check frontend environment variables.

#### Result

After completing this section, you have successfully deployed the frontend of the e-commerce project to AWS.

The results include:

- The Next.js frontend has been built into static files.
- Static files have been uploaded to the Amazon S3 Frontend Bucket.
- CloudFront has distributed the website to the Internet.
- CloudFront invalidation has been created to update the latest version.
- Customers can access the shopping website.
- Admin can access the admin dashboard.
- The frontend can call API Gateway.
- The frontend has connected to Cognito for customer and admin login.
- The website is ready for testing product, cart, order, and payment features.

In the next section, we will configure and test authentication using Amazon Cognito for customers and admins.
