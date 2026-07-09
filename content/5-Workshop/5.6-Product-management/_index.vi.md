---
title: "Quản lý sản phẩm"
date: 2026-05-11
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Mục tiêu

Trong phần này, chúng ta sẽ kiểm thử chức năng quản lý sản phẩm của dự án **Mimi Jewelry E-commerce Platform**.

Chức năng quản lý sản phẩm được sử dụng bởi admin để tạo, cập nhật, xem danh sách sản phẩm và upload hình ảnh sản phẩm lên Amazon S3.

Các nội dung chính trong phần này gồm:

- Kiểm tra trang quản lý sản phẩm trong admin dashboard.
- Kiểm tra API quản lý sản phẩm.
- Tạo sản phẩm mới.
- Upload hình ảnh sản phẩm lên Amazon S3.
- Kiểm tra dữ liệu sản phẩm trong DynamoDB.
- Kiểm tra hình ảnh sản phẩm trong S3 Product Images Bucket.
- Kiểm tra sản phẩm hiển thị ở trang customer.
- Xử lý các lỗi thường gặp khi quản lý sản phẩm.

#### Kiến trúc quản lý sản phẩm

Chức năng quản lý sản phẩm sử dụng nhiều dịch vụ AWS kết hợp với nhau.

Luồng admin tạo sản phẩm:

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

Luồng admin upload hình ảnh sản phẩm:

```text
Admin
   ↓
Admin Product Page
   ↓
API Gateway
   ↓
Lambda tạo presigned URL
   ↓
Upload image to Amazon S3 Product Images Bucket
   ↓
Save image URL to DynamoDB
```

Luồng customer xem sản phẩm:

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

Hình ảnh sản phẩm được lưu trong Amazon S3 và được phân phối thông qua CloudFront.

```text
Product Images Bucket
   ↓
Amazon CloudFront
   ↓
Customer Browser
```

#### Các tài nguyên AWS sử dụng

Chức năng product management sử dụng các tài nguyên chính sau:

- **Amazon Cognito**: xác thực admin.
- **Amazon API Gateway**: nhận request từ frontend.
- **AWS Lambda**: xử lý logic tạo, cập nhật và lấy sản phẩm.
- **Amazon DynamoDB**: lưu dữ liệu sản phẩm.
- **Amazon S3**: lưu hình ảnh sản phẩm.
- **Amazon CloudFront**: phân phối hình ảnh và website.
- **Amazon CloudWatch Logs**: kiểm tra log khi có lỗi.

Thông tin tài nguyên trong workshop:

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

#### Kiểm tra admin đã đăng nhập

Trước khi quản lý sản phẩm, admin cần đăng nhập thành công.

Truy cập trang admin login:

```text
https://dfjng6e5jz4mf.cloudfront.net/admin/login
```

Đăng nhập bằng tài khoản admin đã tạo trong Admin User Pool.

Sau khi đăng nhập thành công, truy cập trang quản lý sản phẩm:

```text
https://dfjng6e5jz4mf.cloudfront.net/admin/products
```

Nếu admin chưa đăng nhập hoặc token hết hạn, hệ thống có thể chuyển về trang login hoặc trả về lỗi:

```text
401 Unauthorized
```

<img src="/images/5-Workshop/5.6-Product-management/admin-login.png" alt="Admin Login" style="max-width: 100%; height: auto;">

#### Mở trang quản lý sản phẩm

Truy cập trang:

```text
https://dfjng6e5jz4mf.cloudfront.net/admin/products
```

Trang này cho phép admin:

- Xem danh sách sản phẩm.
- Tạo sản phẩm mới.
- Cập nhật thông tin sản phẩm.
- Upload hình ảnh sản phẩm.
- Kiểm tra tồn kho.
- Kiểm tra trạng thái sản phẩm.

<img src="/images/5-Workshop/5.6-Product-management/admin-products-page.png" alt="Admin Products Page" style="max-width: 100%; height: auto;">

#### Kiểm tra frontend gọi API danh sách sản phẩm

Mở Developer Tools trên trình duyệt:

```text
F12 → Network
```

Reload trang admin products và kiểm tra request gọi API.

Endpoint thường dùng:

```text
/admin/products
```

Nếu request trả về:

```text
200
```

nghĩa là frontend đã gọi API thành công và admin token hợp lệ.

Nếu request trả về:

```text
401 Unauthorized
```

nghĩa là token bị thiếu, hết hạn hoặc không hợp lệ.

Nếu request trả về:

```text
403 Forbidden
```

nghĩa là tài khoản không có quyền truy cập admin API hoặc authorizer đang cấu hình sai.

#### Tạo sản phẩm mới

Trong trang admin products, bấm nút tạo sản phẩm mới.

Nhập thông tin sản phẩm, ví dụ:

```text
Product Name: Silver Heart Necklace
Category: Necklace
Description: A simple and elegant silver heart necklace for daily wear.
Price: 490000
Stock: 25
Status: Active
```

Sau đó bấm nút lưu sản phẩm.

Khi admin tạo sản phẩm, frontend sẽ gửi request đến backend thông qua API Gateway.

Luồng xử lý:

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

#### Kiểm tra request tạo sản phẩm

Trong Developer Tools, mở tab:

```text
Network
```

Kiểm tra request tạo sản phẩm.

Endpoint:

```text
POST /admin/products
```

Payload mẫu:

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

Response mong đợi:

```json
{
  "success": true,
  "message": "Product created successfully"
}
```

Nếu API trả về thành công, sản phẩm mới sẽ được lưu vào DynamoDB.

#### Kiểm tra sản phẩm trong DynamoDB

Mở AWS Console:

```text
DynamoDB → Tables
```

Chọn bảng:

```text
EcommerceProducts
```

Mở phần:

```text
Explore table items
```

Kiểm tra sản phẩm vừa tạo.

Một item sản phẩm thường có các field như:

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

#### Kiểm tra request tạo presigned URL

Mở Developer Tools:

```text
F12 → Network
```

Khi upload ảnh, kiểm tra request:

```text
POST /admin/uploads/product-image
```

Payload mẫu:

```json
{
  "fileName": "silver-heart-necklace.png",
  "contentType": "image/png"
}
```

Response mong đợi:

```json
{
  "uploadUrl": "https://...",
  "imageUrl": "https://dfjng6e5jz4mf.cloudfront.net/uploads/..."
}
```

Trong đó:

- `uploadUrl` là presigned URL dùng để upload ảnh trực tiếp lên S3.
- `imageUrl` là URL public dùng để hiển thị ảnh sản phẩm trên website.

#### Kiểm tra ảnh trong S3 Product Images Bucket

Mở AWS Console:

```text
Amazon S3 → Buckets
```

Chọn bucket:

```text
ecommerce-aws-backend-productimagesbucket-eqn2cddpurel
```

Kiểm tra thư mục hoặc prefix:

```text
uploads/
```

Sau khi upload thành công, bạn sẽ thấy file ảnh sản phẩm trong bucket.

Ví dụ:

```text
uploads/silver-heart-necklace.png
```

<img src="/images/5-Workshop/5.6-Product-management/s3-product-image.png" alt="S3 Product Image" style="max-width: 100%; height: auto;">

#### Cập nhật thông tin sản phẩm

Admin có thể cập nhật lại thông tin sản phẩm như:

- Tên sản phẩm.
- Mô tả sản phẩm.
- Danh mục.
- Giá bán.
- Số lượng tồn kho.
- Trạng thái sản phẩm.
- Hình ảnh sản phẩm.

Ví dụ cập nhật:

```text
Product Name: Silver Heart Necklace
Price: 450000
Stock: 30
Status: Active
```

Khi cập nhật, frontend sẽ gọi API:

```text
PUT /admin/products
```

hoặc endpoint cập nhật tương ứng trong backend.

<img src="/images/5-Workshop/5.6-Product-management/update-product.png" alt="Update Product" style="max-width: 100%; height: auto;">

#### Kiểm tra dữ liệu sau khi cập nhật

Sau khi cập nhật sản phẩm, kiểm tra lại:

```text
DynamoDB → Tables → EcommerceProducts → Explore table items
```

Đảm bảo các field đã được cập nhật đúng:

```text
price
stock
status
updatedAt
imageUrl
```

Nếu dữ liệu trong DynamoDB đã thay đổi, nghĩa là backend đã cập nhật sản phẩm thành công.

#### Kiểm tra sản phẩm ở trang customer

Sau khi tạo và cập nhật sản phẩm, mở trang customer:

```text
https://dfjng6e5jz4mf.cloudfront.net/products
```

Kiểm tra sản phẩm vừa tạo có hiển thị hay không.

Các thông tin cần kiểm tra:

- Tên sản phẩm.
- Hình ảnh sản phẩm.
- Giá sản phẩm.
- Danh mục sản phẩm.
- Trạng thái hiển thị.
- Nút xem chi tiết hoặc thêm vào giỏ hàng.

<img src="/images/5-Workshop/5.6-Product-management/customer-products-page.png" alt="Customer Products Page" style="max-width: 100%; height: auto;">

#### Kiểm tra trang chi tiết sản phẩm

Chọn sản phẩm vừa tạo để mở trang chi tiết sản phẩm.

URL có thể có dạng:

```text
https://dfjng6e5jz4mf.cloudfront.net/product?id=PRODUCT_ID
```

hoặc route tương ứng của frontend.

Kiểm tra các thông tin:

- Tên sản phẩm.
- Mô tả sản phẩm.
- Giá bán.
- Hình ảnh.
- Tồn kho.
- Nút thêm vào giỏ hàng.

Nếu thông tin hiển thị đúng, nghĩa là API public lấy chi tiết sản phẩm hoạt động tốt.

<img src="/images/5-Workshop/5.6-Product-management/product-detail-page.png" alt="Product Detail Page" style="max-width: 100%; height: auto;">

#### Kiểm tra public product API

Có thể kiểm tra public API bằng trình duyệt hoặc Postman.

Endpoint danh sách sản phẩm:

```text
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod/products
```

Endpoint chi tiết sản phẩm:

```text
https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod/product
```

Nếu API trả về danh sách sản phẩm hoặc chi tiết sản phẩm, nghĩa là backend đã đọc dữ liệu từ DynamoDB thành công.

Response mẫu:

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

#### Kiểm tra CloudWatch Logs khi API lỗi

Nếu tạo sản phẩm, upload ảnh hoặc lấy danh sách sản phẩm bị lỗi, mở CloudWatch Logs:

```text
CloudWatch → Logs → Log groups
```

Chọn log group của Lambda backend.

Kiểm tra log mới nhất.

Một số thông tin cần kiểm tra:

```text
Request path
HTTP method
Request body
Authorization header
Product ID
Error message
Status code
```

#### Một số lỗi thường gặp khi quản lý sản phẩm

##### Admin chưa đăng nhập

Nếu gọi API admin mà chưa đăng nhập, API sẽ trả về:

```text
401 Unauthorized
```

Cách xử lý:

- Đăng nhập lại tài khoản admin.
- Kiểm tra token trong browser storage.
- Kiểm tra request có header `Authorization` hay chưa.

##### Dùng nhầm token customer

Nếu dùng customer token để gọi API admin, API có thể trả về:

```text
403 Forbidden
```

Cách xử lý:

- Đăng xuất tài khoản customer.
- Đăng nhập lại bằng tài khoản admin.
- Kiểm tra frontend đang dùng đúng admin token.

##### Không upload được ảnh

Nếu upload ảnh thất bại, kiểm tra:

- API `/admin/uploads/product-image` có trả về presigned URL không.
- File ảnh có đúng định dạng không.
- S3 bucket có tồn tại không.
- IAM Role của Lambda có quyền tạo presigned URL không.
- CORS của S3 đã được cấu hình đúng chưa.

##### Ảnh upload thành công nhưng không hiển thị

Nếu ảnh đã có trong S3 nhưng website không hiển thị, kiểm tra:

- `imageUrl` đã được lưu vào DynamoDB chưa.
- CloudFront URL có đúng không.
- CloudFront có phân phối đúng path `/uploads` không.
- File ảnh có đúng content type không.
- Browser có cache ảnh cũ không.

##### Sản phẩm không hiển thị ở trang customer

Nếu sản phẩm đã tạo nhưng không hiển thị ở trang customer, kiểm tra:

- Sản phẩm có trạng thái `ACTIVE` không.
- API `/products` có trả về sản phẩm không.
- DynamoDB có dữ liệu sản phẩm không.
- Frontend có gọi đúng API base URL không.
- Browser console có lỗi JavaScript không.

##### Lỗi ghi dữ liệu vào DynamoDB

Nếu API trả về lỗi khi tạo hoặc cập nhật sản phẩm, kiểm tra:

- Lambda IAM Role có quyền ghi vào bảng `EcommerceProducts` không.
- Tên bảng trong biến môi trường Lambda có đúng không.
- Payload gửi lên API có đủ field bắt buộc không.
- CloudWatch Logs có lỗi validation hoặc permission không.

##### Lỗi CORS khi gọi API

Nếu browser báo lỗi CORS khi gọi API, kiểm tra:

- API Gateway có bật CORS không.
- Lambda response có trả về header CORS không.
- Origin frontend có được cho phép không.

Các header thường cần có:

```text
Access-Control-Allow-Origin
Access-Control-Allow-Headers
Access-Control-Allow-Methods
```

#### Kiểm thử product management trên CloudFront URL

Sau khi kiểm thử local thành công, kiểm thử lại trên website đã deploy:

```text
https://dfjng6e5jz4mf.cloudfront.net/admin/products
https://dfjng6e5jz4mf.cloudfront.net/products
```

Các chức năng cần kiểm tra:

- Admin có thể xem danh sách sản phẩm.
- Admin có thể tạo sản phẩm mới.
- Admin có thể cập nhật thông tin sản phẩm.
- Admin có thể upload hình ảnh sản phẩm.
- Hình ảnh được lưu trong S3 Product Images Bucket.
- Sản phẩm được lưu trong DynamoDB.
- Customer có thể xem sản phẩm ở trang products.
- Customer có thể mở trang chi tiết sản phẩm.

#### Kết quả đạt được

Sau khi hoàn thành phần này, hệ thống đã có chức năng quản lý sản phẩm cho admin.

Các kết quả đạt được gồm:

- Admin có thể truy cập trang quản lý sản phẩm.
- Admin có thể tạo sản phẩm mới.
- Admin có thể cập nhật thông tin sản phẩm.
- Admin có thể upload hình ảnh sản phẩm lên Amazon S3.
- Product image URL được lưu trong DynamoDB.
- Sản phẩm được lưu trong bảng `EcommerceProducts`.
- Customer có thể xem danh sách sản phẩm.
- Customer có thể xem chi tiết sản phẩm.
- API public `/products` và `/product` hoạt động.
- API admin `/admin/products` và `/admin/uploads/product-image` hoạt động.
- Hệ thống đã sẵn sàng để kiểm thử chức năng giỏ hàng, đặt hàng và thanh toán.

Ở phần tiếp theo, chúng ta sẽ kiểm thử luồng đặt hàng và thanh toán, bao gồm thêm sản phẩm vào giỏ hàng, checkout, tạo đơn hàng và xử lý thanh toán qua ZaloPay Sandbox.
