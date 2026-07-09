---
title: "Authentication with Amazon Cognito"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Objective

In this section, we will configure and test user authentication for the **Mimi Jewelry E-commerce Platform** project using **Amazon Cognito**.

The system uses Amazon Cognito to manage registration, login, and access control for two main user groups:

- **Customer**: users who purchase products on the website.
- **Admin**: system administrators who manage products, orders, and refunds.

The main contents of this section include:

- Check that Cognito User Pools have been created.
- Configure Cognito information for the frontend.
- Test customer account registration.
- Test customer account confirmation.
- Test customer login.
- Test admin login.
- Check authentication tokens.
- Check API Gateway Authorizer.
- Handle common authentication errors.

#### Authentication architecture

The system uses Amazon Cognito to authenticate users before allowing access to protected APIs.

Customer login flow:

```text
Customer
   ↓
Frontend Login Page
   ↓
Amazon Cognito Customer User Pool
   ↓
JWT Token
   ↓
Frontend stores token
   ↓
Call protected API Gateway endpoints
```

Admin login flow:

```text
Admin
   ↓
Admin Login Page
   ↓
Amazon Cognito Admin User Pool
   ↓
JWT Token
   ↓
Frontend stores token
   ↓
Call admin API Gateway endpoints
```

Authenticated API request flow:

```text
Frontend
   ↓
Authorization: Bearer JWT Token
   ↓
Amazon API Gateway Authorizer
   ↓
AWS Lambda
   ↓
Amazon DynamoDB
```

#### Cognito User Pools in the project

The project uses two separate Cognito User Pools:

- Customer User Pool: used for customers.
- Admin User Pool: used for administrators.

Separating the two User Pools makes access control and security easier to manage.

User Pool information in this workshop:

```text
Customer User Pool ID:
ap-southeast-1_LHSScZri7

Customer User Pool Client ID:
1uijbf07brda1p130293v3prfv

Admin User Pool ID:
ap-southeast-1_k57hCsM4z

Admin User Pool Client ID:
1qc3ttu4q3e95pk7mlqsivr66e
```

#### Check Cognito User Pools in AWS Console

Open AWS Console and go to:

```text
Amazon Cognito → User pools
```

Check the list of User Pools.

You should see the User Pools related to the e-commerce project, for example:

```text
Customer User Pool
Admin User Pool
```

<img src="/images/5-Workshop/5.5-Authentication/cognito-user-pools.png" alt="Cognito User Pools" style="max-width: 100%; height: auto;">

#### Check Customer User Pool

Select the Customer User Pool.

Check the following information:

```text
User pool ID:
ap-southeast-1_LHSScZri7
```

Then open the following section:

```text
App integration → App clients
```

Check the App Client ID:

```text
1uijbf07brda1p130293v3prfv
```

The Customer User Pool is used for:

- Customer account registration.
- Account confirmation.
- Customer login.
- Viewing order history.
- Viewing order details.
- Cancelling orders if the system allows it.

<img src="/images/5-Workshop/5.5-Authentication/customer-user-pool.png" alt="Customer User Pool" style="max-width: 100%; height: auto;">

#### Check Admin User Pool

Go back to the User Pools list and select the Admin User Pool.

Check the User Pool ID:

```text
ap-southeast-1_k57hCsM4z
```

Check the App Client ID:

```text
1qc3ttu4q3e95pk7mlqsivr66e
```

The Admin User Pool is used for:

- Admin login.
- Accessing the admin dashboard.
- Managing products.
- Uploading product images.
- Viewing order lists.
- Viewing order details.
- Managing refunds.

<img src="/images/5-Workshop/5.5-Authentication/admin-user-pool.png" alt="Admin User Pool" style="max-width: 100%; height: auto;">

#### Configure frontend environment variables

The frontend needs environment variables to connect to Cognito.

Open the file:

```text
frontend/.env.local
```

Check the following variables:

```env
NEXT_PUBLIC_AWS_REGION=ap-southeast-1

NEXT_PUBLIC_CUSTOMER_USER_POOL_ID=ap-southeast-1_LHSScZri7
NEXT_PUBLIC_CUSTOMER_USER_POOL_CLIENT_ID=1uijbf07brda1p130293v3prfv

NEXT_PUBLIC_ADMIN_USER_POOL_ID=ap-southeast-1_k57hCsM4z
NEXT_PUBLIC_ADMIN_USER_POOL_CLIENT_ID=1qc3ttu4q3e95pk7mlqsivr66e
```

Notes:

- With Next.js, environment variables used in the browser must start with `NEXT_PUBLIC_`.
- Do not store secrets in the frontend `.env.local` file.
- If the User Pool or App Client ID changes, rebuild the frontend and upload it to S3 again.

<img src="/images/5-Workshop/5.5-Authentication/tao-env.png" alt="Frontend Cognito Environment Variables" style="max-width: 100%; height: auto;">

#### Run frontend locally to test authentication

Move to the frontend folder:

```bash
cd frontend
```

Run the frontend locally:

```bash
npm run dev
```

Open the browser:

```text
http://localhost:3000
```

Authentication pages to test:

```text
http://localhost:3000/account/register
http://localhost:3000/account/login
http://localhost:3000/account/confirm
http://localhost:3000/admin/login
```

#### Test customer registration

Go to the customer registration page:

```text
http://localhost:3000/account/register
```

Enter a new account, for example:

```text
Email: customer-test@example.com
Password: Test@123456
Name: Test Customer
```

Then click the register button.

If registration is successful, the system will create a user in the Customer User Pool.

<img src="/images/5-Workshop/5.5-Authentication/customer-register.png" alt="Customer Register Page" style="max-width: 100%; height: auto;">

#### Check customer user in Cognito

After registration, open AWS Console:

```text
Amazon Cognito → User pools → Customer User Pool → Users
```

Check that the new user has been created.

The user status may be:

```text
UNCONFIRMED
```

or:

```text
CONFIRMED
```

If Cognito requires email confirmation, the user must enter the confirmation code or click the confirmation link before logging in.

<img src="/images/5-Workshop/5.5-Authentication/customer-user-created.png" alt="Customer User Created" style="max-width: 100%; height: auto;">

#### Test customer account confirmation

If the system requires account confirmation, open the email used for registration.

Find the confirmation email from AWS Cognito or from the system.

Then enter the confirmation code on this page:

```text
http://localhost:3000/account/confirm
```

Example:

```text
Email: customer-test@example.com
Confirmation Code: 123456
```

After successful confirmation, the user status in Cognito will change to:

```text
CONFIRMED
```

<img src="/images/5-Workshop/5.5-Authentication/customer-confirm.png" alt="Customer Confirm Account" style="max-width: 100%; height: auto;">

#### Test customer login

Go to the customer login page:

```text
http://localhost:3000/account/login
```

Enter the registered email and password:

```text
Email: customer-test@example.com
Password: Test@123456
```

After successful login, the frontend will receive tokens from Cognito.

The customer can access pages that require login, such as:

```text
/account
/account/orders
/account/order-detail
```

<img src="/images/5-Workshop/5.5-Authentication/customer-login.png" alt="Customer Login Page" style="max-width: 100%; height: auto;">

#### Check customer token in the browser

Open Developer Tools:

```text
F12 → Application
```

Check storage areas such as:

```text
Local Storage
Session Storage
Cookies
```

Depending on how the frontend stores tokens, you may see information such as:

```text
idToken
accessToken
refreshToken
```

This token will be sent with requests to APIs that require authentication.

A common request header looks like this:

```text
Authorization: Bearer <JWT_TOKEN>
```

#### Test customer protected API

After the customer logs in, try opening the order history page:

```text
http://localhost:3000/account/orders
```

Open Developer Tools:

```text
F12 → Network
```

Check API requests such as:

```text
/account/orders
/account/order-detail
```

If the request returns:

```text
200
```

it means the token is valid and API Gateway Authorizer has authenticated it successfully.

If the request returns:

```text
401 Unauthorized
```

it means the token is missing, expired, or invalid.

#### Create or check admin account

Admins should usually not register themselves like customers. Admin accounts should be created directly in the Admin User Pool.

Open AWS Console:

```text
Amazon Cognito → User pools → Admin User Pool → Users
```

Click:

```text
Create user
```

Enter an admin email, for example:

```text
admin@example.com
```

Set a temporary password or permanent password depending on the configuration.

After creation, the admin user will appear in the Admin User Pool.

<img src="/images/5-Workshop/5.5-Authentication/create-admin-user.png" alt="Create Admin User" style="max-width: 100%; height: auto;">

#### Test admin login

Go to the admin login page:

```text
http://localhost:3000/admin/login
```

Enter the admin account:

```text
Email: admin@example.com
Password: Admin@123456
```

After successful login, admin can access:

```text
/admin
/admin/products
/admin/orders
/admin/refunds
```

<img src="/images/5-Workshop/5.5-Authentication/admin-login.png" alt="Admin Login Page" style="max-width: 100%; height: auto;">

#### Check admin protected API calls

After admin logs in, open:

```text
http://localhost:3000/admin/products
```

Open Developer Tools:

```text
F12 → Network
```

Check admin APIs:

```text
/admin/products
/admin/orders
/admin/order-detail
/admin/refunds
/admin/uploads/product-image
```

If the API returns:

```text
200
```

it means the admin token is valid.

If the API returns:

```text
401 Unauthorized
```

it means the token is missing or expired.

If the API returns:

```text
403 Forbidden
```

it means the user is logged in but does not have permission to access that endpoint, or the authorizer is using the wrong User Pool.

#### API Gateway Authorizer

In the backend, API Gateway uses Cognito JWT Authorizer to protect endpoints that require login.

Public endpoints usually do not require a token:

```text
/products
/product
/cart
/checkout
/payments/zalopay/callback
```

Customer endpoints require a customer token:

```text
/account/register
/account/login
/account/orders
/account/order-detail
/account/cancel-order
```

Admin endpoints require an admin token:

```text
/admin/products
/admin/orders
/admin/order-detail
/admin/refunds
/admin/uploads/product-image
```

API Gateway authentication flow:

```text
Frontend sends request
   ↓
Authorization header contains JWT token
   ↓
API Gateway validates token with Cognito User Pool
   ↓
If token is valid, request is forwarded to Lambda
   ↓
Lambda processes business logic
```

#### Check Authorizer in AWS Console

Open AWS Console:

```text
API Gateway → APIs
```

Select the API of the e-commerce project.

Check the section:

```text
Authorization / Authorizers
```

You should see an authorizer related to Cognito or JWT.

Check the following:

- Customer authorizer uses the correct Customer User Pool.
- Admin authorizer uses the correct Admin User Pool.
- Customer routes are attached to the customer authorizer.
- Admin routes are attached to the admin authorizer.
- Public routes do not require an authorizer.

<img src="images/5-Workshop/5.5-Authentication/authorizers.png" alt="API Gateway Authorizers" style="max-width: 100%; height: auto;">

#### Check CloudWatch Logs when login fails

If login or API calls fail, open CloudWatch Logs:

```text
CloudWatch → Logs → Log groups
```

Select the log group of the backend Lambda function.

Check the latest log stream to see the error.

Some information to check:

```text
Request path
HTTP method
Authorization header
User identity
Error message
Status code
```

#### Common authentication errors

##### User is not confirmed

If the customer cannot log in, check the user status in Cognito.

If the user status is:

```text
UNCONFIRMED
```

the account must be confirmed first.

How to fix:

- Check the confirmation email.
- Enter the confirmation code on the confirmation page.
- Or manually confirm the user in Cognito when testing.

##### Wrong User Pool ID or Client ID

If the frontend cannot log in or shows a configuration error, check the file:

```text
frontend/.env.local
```

Make sure the following variables are correct:

```text
NEXT_PUBLIC_CUSTOMER_USER_POOL_ID
NEXT_PUBLIC_CUSTOMER_USER_POOL_CLIENT_ID
NEXT_PUBLIC_ADMIN_USER_POOL_ID
NEXT_PUBLIC_ADMIN_USER_POOL_CLIENT_ID
```

After editing, rebuild the frontend:

```bash
npm run build
```

##### Token expired

If the website has been used for a while and the API returns:

```text
401 Unauthorized
```

the token may have expired.

How to fix:

- Log in again.
- Check the refresh token flow if the system supports it.
- Clear the old token in browser storage and log in again.

##### Using customer token for admin endpoint

If a customer token is used to call an admin endpoint, the API may return:

```text
401 Unauthorized
```

or:

```text
403 Forbidden
```

How to fix:

- Log out as customer.
- Log in using an admin account.
- Check that the frontend uses the correct admin token when calling `/admin/*`.

##### Admin has not changed temporary password

If the admin was created with a temporary password, Cognito may require a password change during the first login.

How to fix:

- Log in as admin.
- Complete the password change step if required.
- Or reset the password in Cognito Console when testing.

##### API route is not attached to the correct authorizer

If a protected endpoint can still be accessed without login, the route may not be attached to an authorizer.

How to fix:

- Check `template.yaml`.
- Check the route in API Gateway.
- Make sure customer routes use the customer authorizer.
- Make sure admin routes use the admin authorizer.
- Redeploy the backend after making changes.

#### Test authentication on CloudFront URL

After local testing is successful, test again on the deployed website:

```text
https://dfjng6e5jz4mf.cloudfront.net/account/register
https://dfjng6e5jz4mf.cloudfront.net/account/login
https://dfjng6e5jz4mf.cloudfront.net/admin/login
```

Features to check:

- Customers can register.
- Customers can confirm their account.
- Customers can log in.
- Customers can view order history.
- Admin can log in.
- Admin can access the admin dashboard.
- Users who are not logged in cannot access pages that require authentication.
- Tokens are sent correctly in API requests.

#### Result

After completing this section, the system has authentication functionality using Amazon Cognito.

The results include:

- Customer User Pool has been checked.
- Admin User Pool has been checked.
- Frontend has been configured with Cognito User Pool ID and App Client ID.
- Customers can register accounts.
- Customers can confirm accounts.
- Customers can log in.
- Admin can log in.
- Frontend can store and send JWT tokens.
- API Gateway Authorizer can validate tokens from Cognito.
- Customer endpoints are protected.
- Admin endpoints are protected.
- The system is ready to test product management, order placement, and payment features.

In the next section, we will test product management features, including creating products, updating products, and uploading product images to Amazon S3.
