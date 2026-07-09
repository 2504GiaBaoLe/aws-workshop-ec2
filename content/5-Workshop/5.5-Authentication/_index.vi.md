---
title: "Authentication với Amazon Cognito"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Mục tiêu

Trong phần này, chúng ta sẽ cấu hình và kiểm thử chức năng xác thực người dùng cho dự án **Mimi Jewelry E-commerce Platform** bằng **Amazon Cognito**.

Hệ thống sử dụng Amazon Cognito để quản lý đăng ký, đăng nhập và phân quyền truy cập cho hai nhóm người dùng chính:

- **Customer**: người dùng mua hàng trên website.
- **Admin**: người quản trị hệ thống, quản lý sản phẩm, đơn hàng và hoàn tiền.

Các nội dung chính trong phần này gồm:

- Kiểm tra Cognito User Pools đã được tạo.
- Cấu hình thông tin Cognito cho frontend.
- Kiểm thử đăng ký tài khoản customer.
- Kiểm thử xác nhận tài khoản customer.
- Kiểm thử đăng nhập customer.
- Kiểm thử đăng nhập admin.
- Kiểm tra token xác thực.
- Kiểm tra API Gateway Authorizer.
- Xử lý các lỗi authentication thường gặp.

#### Kiến trúc authentication

Hệ thống sử dụng Amazon Cognito để xác thực người dùng trước khi cho phép truy cập các API bảo vệ.

Luồng đăng nhập customer:

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

Luồng đăng nhập admin:

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

Luồng gọi API có xác thực:

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

#### Cognito User Pools trong dự án

Dự án sử dụng hai Cognito User Pools riêng biệt:

- Customer User Pool: dành cho người dùng mua hàng.
- Admin User Pool: dành cho người quản trị.

Việc tách riêng hai User Pool giúp hệ thống dễ quản lý quyền truy cập và bảo mật hơn.

Thông tin User Pool trong workshop:

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

#### Kiểm tra Cognito User Pools trên AWS Console

Mở AWS Console và vào dịch vụ:

```text
Amazon Cognito → User pools
```

Kiểm tra danh sách User Pools.

Bạn sẽ thấy các User Pool liên quan đến dự án ecommerce, ví dụ:

```text
Customer User Pool
Admin User Pool
```

<img src="/images/5-Workshop/5.5-Authentication/cognito-user-pools.png" alt="Cognito User Pools" style="max-width: 100%; height: auto;">

#### Kiểm tra Customer User Pool

Chọn Customer User Pool.

Kiểm tra thông tin:

```text
User pool ID:
ap-southeast-1_LHSScZri7
```

Sau đó mở tab hoặc phần:

```text
App integration → App clients
```

Kiểm tra App Client ID:

```text
1uijbf07brda1p130293v3prfv
```

Customer User Pool được dùng cho các chức năng:

- Đăng ký tài khoản khách hàng.
- Xác nhận tài khoản.
- Đăng nhập customer.
- Xem lịch sử đơn hàng.
- Xem chi tiết đơn hàng.
- Hủy đơn hàng nếu hệ thống cho phép.

<img src="/images/5-Workshop/5.5-Authentication/customer-user-pool.png" alt="Customer User Pool" style="max-width: 100%; height: auto;">

#### Kiểm tra Admin User Pool

Quay lại danh sách User Pools và chọn Admin User Pool.

Kiểm tra User Pool ID:

```text
ap-southeast-1_k57hCsM4z
```

Kiểm tra App Client ID:

```text
1qc3ttu4q3e95pk7mlqsivr66e
```

Admin User Pool được dùng cho các chức năng:

- Đăng nhập admin.
- Truy cập admin dashboard.
- Quản lý sản phẩm.
- Upload hình ảnh sản phẩm.
- Xem danh sách đơn hàng.
- Xem chi tiết đơn hàng.
- Quản lý hoàn tiền.

<img src="/images/5-Workshop/5.5-Authentication/admin-user-pool.png" alt="Admin User Pool" style="max-width: 100%; height: auto;">

#### Cấu hình biến môi trường frontend

Frontend cần các biến môi trường để kết nối đến Cognito.

Mở file:

```text
frontend/.env.local
```

Kiểm tra các biến sau:

```env
NEXT_PUBLIC_AWS_REGION=ap-southeast-1

NEXT_PUBLIC_CUSTOMER_USER_POOL_ID=ap-southeast-1_LHSScZri7
NEXT_PUBLIC_CUSTOMER_USER_POOL_CLIENT_ID=1uijbf07brda1p130293v3prfv

NEXT_PUBLIC_ADMIN_USER_POOL_ID=ap-southeast-1_k57hCsM4z
NEXT_PUBLIC_ADMIN_USER_POOL_CLIENT_ID=1qc3ttu4q3e95pk7mlqsivr66e
```

Lưu ý:

- Với Next.js, biến môi trường dùng trên browser phải bắt đầu bằng `NEXT_PUBLIC_`.
- Không lưu secret trong file frontend `.env.local`.
- Nếu thay đổi User Pool hoặc App Client ID, cần build lại frontend và upload lại lên S3.

<img src="/images/5-Workshop/5.5-Authentication/tao-env.png" alt="Frontend Cognito Environment Variables" style="max-width: 100%; height: auto;">

#### Chạy frontend local để kiểm thử authentication

Di chuyển vào thư mục frontend:

```bash
cd frontend
```

Chạy frontend local:

```bash
npm run dev
```

Mở trình duyệt:

```text
http://localhost:3000
```

Các trang authentication cần kiểm thử:

```text
http://localhost:3000/account/register
http://localhost:3000/account/login
http://localhost:3000/account/confirm
http://localhost:3000/admin/login
```

#### Kiểm thử đăng ký customer

Truy cập trang đăng ký customer:

```text
http://localhost:3000/account/register
```

Nhập thông tin tài khoản mới, ví dụ:

```text
Email: customer-test@example.com
Password: Test@123456
Name: Test Customer
```

Sau đó bấm nút đăng ký.

Nếu đăng ký thành công, hệ thống sẽ tạo user trong Customer User Pool.

<img src="/images/5-Workshop/5.5-Authentication/customer-register.png" alt="Customer Register Page" style="max-width: 100%; height: auto;">

#### Kiểm tra customer trong Cognito

Sau khi đăng ký, vào AWS Console:

```text
Amazon Cognito → User pools → Customer User Pool → Users
```

Kiểm tra user mới đã được tạo.

Trạng thái user có thể là:

```text
UNCONFIRMED
```

hoặc:

```text
CONFIRMED
```

Nếu Cognito yêu cầu xác nhận email, user cần nhập mã xác nhận hoặc bấm link xác nhận trước khi đăng nhập.

<img src="/images/5-Workshop/5.5-Authentication/customer-user-created.png" alt="Customer User Created" style="max-width: 100%; height: auto;">

#### Kiểm thử xác nhận tài khoản customer

Nếu hệ thống yêu cầu xác nhận tài khoản, mở email đã dùng để đăng ký.

Tìm email xác nhận từ AWS Cognito hoặc từ hệ thống.

Sau đó nhập mã xác nhận tại trang:

```text
http://localhost:3000/account/confirm
```

Ví dụ:

```text
Email: customer-test@example.com
Confirmation Code: 123456
```

Sau khi xác nhận thành công, trạng thái user trong Cognito sẽ chuyển thành:

```text
CONFIRMED
```

<img src="/images/5-Workshop/5.5-Authentication/customer-confirm.png" alt="Customer Confirm Account" style="max-width: 100%; height: auto;">

#### Kiểm thử đăng nhập customer

Truy cập trang đăng nhập customer:

```text
http://localhost:3000/account/login
```

Nhập email và password đã đăng ký:

```text
Email: customer-test@example.com
Password: Test@123456
```

Sau khi đăng nhập thành công, frontend sẽ nhận được token từ Cognito.

Customer có thể truy cập các trang yêu cầu đăng nhập như:

```text
/account
/account/orders
/account/order-detail
```

<img src="/images/5-Workshop/5.5-Authentication/customer-login.png" alt="Customer Login Page" style="max-width: 100%; height: auto;">

#### Kiểm tra customer token trên browser

Mở Developer Tools:

```text
F12 → Application
```

Kiểm tra ở các khu vực như:

```text
Local Storage
Session Storage
Cookies
```

Tùy theo cách frontend lưu token, bạn có thể thấy các thông tin như:

```text
idToken
accessToken
refreshToken
```

Token này sẽ được frontend gửi kèm khi gọi API cần xác thực.

Dạng request thường có header:

```text
Authorization: Bearer <JWT_TOKEN>
```

#### Kiểm thử API customer yêu cầu đăng nhập

Sau khi customer đăng nhập, thử truy cập trang lịch sử đơn hàng:

```text
http://localhost:3000/account/orders
```

Mở Developer Tools:

```text
F12 → Network
```

Kiểm tra request gọi API:

```text
/account/orders
/account/order-detail
```

Nếu request trả về:

```text
200
```

nghĩa là token hợp lệ và API Gateway Authorizer đã xác thực thành công.

Nếu request trả về:

```text
401 Unauthorized
```

nghĩa là token bị thiếu, hết hạn hoặc không hợp lệ.

#### Tạo hoặc kiểm tra tài khoản admin

Admin thường không nên tự đăng ký như customer. Tài khoản admin nên được tạo trực tiếp trong Admin User Pool.

Vào AWS Console:

```text
Amazon Cognito → User pools → Admin User Pool → Users
```

Bấm:

```text
Create user
```

Nhập email admin, ví dụ:

```text
admin@example.com
```

Thiết lập password tạm thời hoặc password cố định tùy cấu hình.

Sau khi tạo, user admin sẽ xuất hiện trong Admin User Pool.

<img src="/images/5-Workshop/5.5-Authentication/create-admin-user.png" alt="Create Admin User" style="max-width: 100%; height: auto;">

#### Kiểm thử đăng nhập admin

Truy cập trang đăng nhập admin:

```text
http://localhost:3000/admin/login
```

Nhập tài khoản admin đã tạo:

```text
Email: admin@example.com
Password: Admin@123456
```

Sau khi đăng nhập thành công, admin có thể truy cập:

```text
/admin
/admin/products
/admin/orders
/admin/refunds
```

<img src="/images/5-Workshop/5.5-Authentication/admin-login.png" alt="Admin Login Page" style="max-width: 100%; height: auto;">

#### Kiểm tra admin gọi API protected

Sau khi admin đăng nhập, mở trang:

```text
http://localhost:3000/admin/products
```

Mở Developer Tools:

```text
F12 → Network
```

Kiểm tra các API admin:

```text
/admin/products
/admin/orders
/admin/order-detail
/admin/refunds
/admin/uploads/product-image
```

Nếu API trả về:

```text
200
```

nghĩa là admin token hợp lệ.

Nếu API trả về:

```text
401 Unauthorized
```

nghĩa là token thiếu hoặc hết hạn.

Nếu API trả về:

```text
403 Forbidden
```

nghĩa là user đã đăng nhập nhưng không có quyền truy cập endpoint đó hoặc authorizer đang dùng sai User Pool.

#### API Gateway Authorizer

Trong backend, API Gateway sử dụng Cognito JWT Authorizer để bảo vệ các endpoint cần đăng nhập.

Các endpoint public thường không yêu cầu token:

```text
/products
/product
/cart
/checkout
/payments/zalopay/callback
```

Các endpoint customer yêu cầu customer token:

```text
/account/register
/account/login
/account/orders
/account/order-detail
/account/cancel-order
```

Các endpoint admin yêu cầu admin token:

```text
/admin/products
/admin/orders
/admin/order-detail
/admin/refunds
/admin/uploads/product-image
```

Luồng xác thực API Gateway:

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

#### Kiểm tra Authorizer trên AWS Console

Vào AWS Console:

```text
API Gateway → APIs
```

Chọn API của dự án ecommerce.

Kiểm tra phần:

```text
Authorization / Authorizers
```

Bạn sẽ thấy authorizer liên quan đến Cognito hoặc JWT.

Kiểm tra:

- Customer authorizer dùng đúng Customer User Pool.
- Admin authorizer dùng đúng Admin User Pool.
- Các route customer được gắn customer authorizer.
- Các route admin được gắn admin authorizer.
- Các route public không yêu cầu authorizer.

<img src="/images/5-Workshop/5.5-Authentication/authorizers.png" alt="API Gateway Authorizers" style="max-width: 100%; height: auto;">

#### Kiểm tra CloudWatch Logs khi đăng nhập lỗi

Nếu đăng nhập hoặc gọi API bị lỗi, mở CloudWatch Logs:

```text
CloudWatch → Logs → Log groups
```

Chọn log group của Lambda backend.

Kiểm tra log mới nhất để xem lỗi.

Một số thông tin cần kiểm tra:

```text
Request path
HTTP method
Authorization header
User identity
Error message
Status code
```

#### Một số lỗi authentication thường gặp

##### User chưa confirm

Nếu customer đăng nhập nhưng bị lỗi, kiểm tra trạng thái user trong Cognito.

Nếu user đang là:

```text
UNCONFIRMED
```

cần xác nhận tài khoản trước.

Cách xử lý:

- Kiểm tra email xác nhận.
- Nhập mã xác nhận tại trang confirm.
- Hoặc confirm user thủ công trong Cognito nếu đang test.

##### Sai User Pool ID hoặc Client ID

Nếu frontend không đăng nhập được hoặc báo lỗi cấu hình, kiểm tra lại file:

```text
frontend/.env.local
```

Đảm bảo đúng các biến:

```text
NEXT_PUBLIC_CUSTOMER_USER_POOL_ID
NEXT_PUBLIC_CUSTOMER_USER_POOL_CLIENT_ID
NEXT_PUBLIC_ADMIN_USER_POOL_ID
NEXT_PUBLIC_ADMIN_USER_POOL_CLIENT_ID
```

Sau khi sửa, cần build lại frontend:

```bash
npm run build
```

##### Token hết hạn

Nếu đang dùng website một thời gian rồi API trả về:

```text
401 Unauthorized
```

có thể token đã hết hạn.

Cách xử lý:

- Đăng nhập lại.
- Kiểm tra refresh token flow nếu hệ thống có hỗ trợ.
- Xóa token cũ trong browser storage rồi login lại.

##### Dùng nhầm token customer cho admin

Nếu dùng customer token gọi endpoint admin, API có thể trả về:

```text
401 Unauthorized
```

hoặc:

```text
403 Forbidden
```

Cách xử lý:

- Đăng xuất customer.
- Đăng nhập bằng tài khoản admin.
- Kiểm tra frontend đang dùng đúng token admin khi gọi `/admin/*`.

##### Admin chưa đổi password tạm thời

Nếu admin được tạo bằng temporary password, Cognito có thể yêu cầu đổi password ở lần đăng nhập đầu tiên.

Cách xử lý:

- Đăng nhập admin.
- Thực hiện bước đổi password nếu hệ thống yêu cầu.
- Hoặc đặt lại password trong Cognito Console khi test.

##### API route chưa gắn authorizer đúng

Nếu endpoint protected vẫn truy cập được khi chưa đăng nhập, có thể route chưa gắn authorizer.

Cách xử lý:

- Kiểm tra `template.yaml`.
- Kiểm tra route trong API Gateway.
- Đảm bảo route customer dùng customer authorizer.
- Đảm bảo route admin dùng admin authorizer.
- Deploy lại backend sau khi sửa.

#### Kiểm thử authentication trên CloudFront URL

Sau khi đã kiểm thử local thành công, kiểm thử lại trên website đã deploy:

```text
https://dfjng6e5jz4mf.cloudfront.net/account/register
https://dfjng6e5jz4mf.cloudfront.net/account/login
https://dfjng6e5jz4mf.cloudfront.net/admin/login
```

Các chức năng cần kiểm tra:

- Customer có thể đăng ký.
- Customer có thể xác nhận tài khoản.
- Customer có thể đăng nhập.
- Customer có thể xem lịch sử đơn hàng.
- Admin có thể đăng nhập.
- Admin có thể truy cập trang quản trị.
- Người chưa đăng nhập không thể truy cập trang yêu cầu authentication.
- Token được gửi đúng trong request API.

#### Kết quả đạt được

Sau khi hoàn thành phần này, hệ thống đã có chức năng authentication bằng Amazon Cognito.

Các kết quả đạt được gồm:

- Customer User Pool đã được kiểm tra.
- Admin User Pool đã được kiểm tra.
- Frontend đã cấu hình Cognito User Pool ID và App Client ID.
- Customer có thể đăng ký tài khoản.
- Customer có thể xác nhận tài khoản.
- Customer có thể đăng nhập.
- Admin có thể đăng nhập.
- Frontend có thể lưu và gửi JWT token.
- API Gateway Authorizer có thể xác thực token từ Cognito.
- Các endpoint customer được bảo vệ.
- Các endpoint admin được bảo vệ.
- Hệ thống đã sẵn sàng để kiểm thử chức năng quản lý sản phẩm, đặt hàng và thanh toán.

Ở phần tiếp theo, chúng ta sẽ kiểm thử chức năng quản lý sản phẩm, bao gồm tạo sản phẩm, cập nhật sản phẩm và upload hình ảnh sản phẩm lên Amazon S3.
