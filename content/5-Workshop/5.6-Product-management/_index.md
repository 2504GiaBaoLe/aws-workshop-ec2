---
title: "Product Management"
date: 2026-05-11
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Objective

In this section, we will test the product management feature of the **Mimi Jewelry E-commerce Platform** project.

The product management feature is used by admins to create products, update products, view the product list, and upload product images to Amazon S3.

The main contents of this section include:

- Check the product management page in the admin dashboard.
- Check the product management API.
- Create a new product.
- Upload product images to Amazon S3.
- Check product data in DynamoDB.
- Check product images in the S3 Product Images Bucket.
- Check whether products are displayed on the customer page.
- Handle common errors when managing products.

#### Product management architecture

The product management feature uses multiple AWS services together.

Admin product creation flow:

```text
Admin
   ↓
Admin Product Page
   ↓
Amazon API Gateway
   ↓
AWS Lambda
   ↓
Amazon DynamoDB Products Table
```

Admin product image upload flow:

```text
Admin
   ↓
Admin Product Page
   ↓
API Gateway
   ↓
Lambda creates presigned URL
   ↓
Upload image to Amazon S3 Product Images Bucket
   ↓
Save image URL to DynamoDB
```

Customer product viewing flow:

```text
Customer
   ↓
Frontend Product Page
   ↓
Amazon API Gateway
   ↓
AWS Lambda
   ↓
Amazon DynamoDB Products Table
```

Product images are stored in Amazon S3 and distributed through CloudFront.

```text
Product Images Bucket
   ↓
Amazon CloudFront
   ↓
Customer Browser
```

#### AWS resources used

The product management feature uses the following main resources:

- **Amazon Cognito**: authenticates admin users.
- **Amazon API Gateway**: receives requests from the frontend.
- **AWS Lambda**: handles product creation, update, and retrieval logic.
- **Amazon DynamoDB**: stores product data.
- **Amazon S3**: stores product images.
- **Amazon CloudFront**: distributes images and the website.
- **Amazon CloudWatch Logs**: checks logs when errors occur.

Resource information in this workshop:

```text
Products Table:
EcommerceProducts

Product Images Bucket:
ecommerce-aws-backend-productimagesbucket-eqn2cddpurel

API Gateway URL:
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod

CloudFront URL:
https://dfjng6e5jz4mf.cloudfront.net
```

#### Check that admin is logged in

Before managing products, the admin must log in successfully.

Access the admin login page:

```text
https://dfjng6e5jz4mf.cloudfront.net/admin/login
```

Log in using the admin account created in the Admin User Pool.

After logging in successfully, access the product management page:

```text
https://dfjng6e5jz4mf.cloudfront.net/admin/products
```

If the admin is not logged in or the token has expired, the system may redirect to the login page or return the following error:

```text
401 Unauthorized
```

<img src="/images/5-Workshop/5.6-Product-management/admin-login.png" alt="Admin Login" style="max-width: 100%; height: auto;">

#### Open the product management page

Access the following page:

```text
https://dfjng6e5jz4mf.cloudfront.net/admin/products
```

This page allows admin users to:

- View the product list.
- Create a new product.
- Update product information.
- Upload product images.
- Check inventory.
- Check product status.

<img src="/images/5-Workshop/5.6-Product-management/admin-products-page.png" alt="Admin Products Page" style="max-width: 100%; height: auto;">

#### Check frontend API call for product list

Open Developer Tools in the browser:

```text
F12 → Network
```

Reload the admin products page and check the API request.

Common endpoint:

```text
/admin/products
```

If the request returns:

```text
200
```

it means the frontend called the API successfully and the admin token is valid.

If the request returns:

```text
401 Unauthorized
```

it means the token is missing, expired, or invalid.

If the request returns:

```text
403 Forbidden
```

it means the account does not have permission to access the admin API or the authorizer is configured incorrectly.

#### Create a new product

On the admin products page, click the button to create a new product.

Enter product information, for example:

```text
Product Name: Silver Heart Necklace
Category: Necklace
Description: A simple and elegant silver heart necklace for daily wear.
Price: 490000
Stock: 25
Status: Active
```

Then click the save button.

When the admin creates a product, the frontend sends a request to the backend through API Gateway.

Processing flow:

```text
Admin Product Form
   ↓
POST /admin/products
   ↓
API Gateway
   ↓
Lambda
   ↓
DynamoDB Products Table
```

<img src="/images/5-Workshop/5.6-Product-management/create-product-form.png" alt="Create Product Form" style="max-width: 100%; height: auto;">

#### Check product creation request

In Developer Tools, open the following tab:

```text
Network
```

Check the product creation request.

Endpoint:

```text
POST /admin/products
```

Sample payload:

```json
{
  "name": "Silver Heart Necklace",
  "category": "Necklace",
  "description": "A simple and elegant silver heart necklace for daily wear.",
  "price": 490000,
  "stock": 25,
  "status": "ACTIVE"
}
```

Expected response:

```json
{
  "success": true,
  "message": "Product created successfully"
}
```

If the API returns a successful response, the new product will be saved to DynamoDB.

#### Check product in DynamoDB

Open AWS Console:

```text
DynamoDB → Tables
```

Select the table:

```text
EcommerceProducts
```

Open:

```text
Explore table items
```

Check the product that was just created.

A product item usually contains fields such as:

```text
productId
name
category
description
price
stock
status
imageUrl
createdAt
updatedAt
```

<img src="/images/5-Workshop/5.6-Product-management/dynamodb-product-created.png" alt="DynamoDB Product Created" style="max-width: 100%; height: auto;">

#### Check presigned URL request

Open Developer Tools:

```text
F12 → Network
```

When uploading an image, check the following request:

```text
POST /admin/uploads/product-image
```

Sample payload:

```json
{
  "fileName": "silver-heart-necklace.png",
  "contentType": "image/png"
}
```

Expected response:

```json
{
  "uploadUrl": "https://...",
  "imageUrl": "https://dfjng6e5jz4mf.cloudfront.net/uploads/..."
}
```

Where:

- `uploadUrl` is the presigned URL used to upload the image directly to S3.
- `imageUrl` is the public URL used to display the product image on the website.

#### Check image in S3 Product Images Bucket

Open AWS Console:

```text
Amazon S3 → Buckets
```

Select the bucket:

```text
ecommerce-aws-backend-productimagesbucket-eqn2cddpurel
```

Check the folder or prefix:

```text
uploads/
```

After the upload succeeds, you will see the product image file in the bucket.

Example:

```text
uploads/silver-heart-necklace.png
```

<img src="/images/5-Workshop/5.6-Product-management/s3-product-image.png" alt="S3 Product Image" style="max-width: 100%; height: auto;">

#### Update product information

Admin can update product information such as:

- Product name.
- Product description.
- Category.
- Price.
- Stock quantity.
- Product status.
- Product image.

Example update:

```text
Product Name: Silver Heart Necklace
Price: 450000
Stock: 30
Status: Active
```

When updating a product, the frontend will call the API:

```text
PUT /admin/products
```

or the corresponding update endpoint in the backend.

<img src="/images/5-Workshop/5.6-Product-management/update-product.png" alt="Update Product" style="max-width: 100%; height: auto;">

#### Check data after update

After updating the product, check again:

```text
DynamoDB → Tables → EcommerceProducts → Explore table items
```

Make sure the following fields have been updated correctly:

```text
price
stock
status
updatedAt
imageUrl
```

If the data in DynamoDB has changed, it means the backend updated the product successfully.

#### Check product on customer page

After creating and updating the product, open the customer product page:

```text
https://dfjng6e5jz4mf.cloudfront.net/products
```

Check whether the product you created is displayed.

Information to check:

- Product name.
- Product image.
- Product price.
- Product category.
- Display status.
- View detail button or add to cart button.

<img src="/images/5-Workshop/5.6-Product-management/customer-products-page.png" alt="Customer Products Page" style="max-width: 100%; height: auto;">

#### Check product detail page

Select the product you created to open the product detail page.

The URL may look like this:

```text
https://dfjng6e5jz4mf.cloudfront.net/product?id=PRODUCT_ID
```

or another route used by the frontend.

Check the following information:

- Product name.
- Product description.
- Price.
- Image.
- Stock.
- Add to cart button.

If the information is displayed correctly, it means the public product detail API is working properly.

<img src="/images/5-Workshop/5.6-Product-management/product-detail-page.png" alt="Product Detail Page" style="max-width: 100%; height: auto;">

#### Check public product API

You can check the public API using a browser or Postman.

Product list endpoint:

```text
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod/products
```

Product detail endpoint:

```text
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod/product
```

If the API returns the product list or product details, it means the backend has successfully read data from DynamoDB.

Sample response:

```json
[
  {
    "productId": "product-001",
    "name": "Silver Heart Necklace",
    "category": "Necklace",
    "price": 450000,
    "stock": 30,
    "status": "ACTIVE",
    "imageUrl": "https://dfjng6e5jz4mf.cloudfront.net/uploads/silver-heart-necklace.png"
  }
]
```

#### Check CloudWatch Logs when API errors occur

If product creation, image upload, or product listing fails, open CloudWatch Logs:

```text
CloudWatch → Logs → Log groups
```

Select the log group of the backend Lambda function.

Check the latest logs.

Information to check:

```text
Request path
HTTP method
Request body
Authorization header
Product ID
Error message
Status code
```

#### Common errors when managing products

##### Admin is not logged in

If an admin API is called without login, the API will return:

```text
401 Unauthorized
```

How to fix:

- Log in again using the admin account.
- Check the token in browser storage.
- Check whether the request contains the `Authorization` header.

##### Using the wrong customer token

If a customer token is used to call an admin API, the API may return:

```text
403 Forbidden
```

How to fix:

- Log out of the customer account.
- Log in again using the admin account.
- Check that the frontend is using the correct admin token.

##### Image upload fails

If image upload fails, check:

- Whether the `/admin/uploads/product-image` API returns a presigned URL.
- Whether the image file format is valid.
- Whether the S3 bucket exists.
- Whether the Lambda IAM Role has permission to create a presigned URL.
- Whether S3 CORS is configured correctly.

##### Image uploaded successfully but not displayed

If the image exists in S3 but is not displayed on the website, check:

- Whether `imageUrl` has been saved to DynamoDB.
- Whether the CloudFront URL is correct.
- Whether CloudFront distributes the `/uploads` path correctly.
- Whether the image file has the correct content type.
- Whether the browser is caching an old image.

##### Product is not displayed on the customer page

If the product was created but is not displayed on the customer page, check:

- Whether the product status is `ACTIVE`.
- Whether the `/products` API returns the product.
- Whether DynamoDB contains the product data.
- Whether the frontend calls the correct API base URL.
- Whether the browser console shows any JavaScript errors.

##### DynamoDB write error

If the API returns an error when creating or updating a product, check:

- Whether the Lambda IAM Role has permission to write to the `EcommerceProducts` table.
- Whether the table name in the Lambda environment variables is correct.
- Whether the API payload contains all required fields.
- Whether CloudWatch Logs show validation or permission errors.

##### CORS error when calling API

If the browser shows a CORS error when calling the API, check:

- Whether API Gateway has CORS enabled.
- Whether the Lambda response returns CORS headers.
- Whether the frontend origin is allowed.

Common required headers:

```text
Access-Control-Allow-Origin
Access-Control-Allow-Headers
Access-Control-Allow-Methods
```

#### Test product management on CloudFront URL

After local testing is successful, test again on the deployed website:

```text
https://dfjng6e5jz4mf.cloudfront.net/admin/products
https://dfjng6e5jz4mf.cloudfront.net/products
```

Features to check:

- Admin can view the product list.
- Admin can create a new product.
- Admin can update product information.
- Admin can upload product images.
- Images are stored in the S3 Product Images Bucket.
- Products are stored in DynamoDB.
- Customers can view products on the products page.
- Customers can open the product detail page.

#### Result

After completing this section, the system has product management functionality for admins.

The results include:

- Admin can access the product management page.
- Admin can create new products.
- Admin can update product information.
- Admin can upload product images to Amazon S3.
- Product image URLs are saved in DynamoDB.
- Products are stored in the `EcommerceProducts` table.
- Customers can view the product list.
- Customers can view product details.
- Public APIs `/products` and `/product` are working.
- Admin APIs `/admin/products` and `/admin/uploads/product-image` are working.
- The system is ready to test cart, order, and payment features.

In the next section, we will test the order and payment flow, including adding products to the cart, checkout, creating orders, and processing payment through ZaloPay Sandbox.
