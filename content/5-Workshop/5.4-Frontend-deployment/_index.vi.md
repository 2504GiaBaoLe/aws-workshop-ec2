---
title: "Triển khai Frontend"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Mục tiêu

Trong phần này, chúng ta sẽ triển khai frontend của dự án **Mimi Jewelry E-commerce Platform**.

Frontend của dự án được xây dựng bằng **Next.js** và được deploy theo mô hình static website. Sau khi build, các file tĩnh sẽ được upload lên **Amazon S3 Frontend Bucket** và được phân phối ra Internet thông qua **Amazon CloudFront**.

Các nội dung chính trong phần này gồm:

- Cấu hình biến môi trường cho frontend.
- Build Next.js frontend.
- Upload static files lên Amazon S3.
- Tạo CloudFront invalidation.
- Kiểm tra website sau khi deploy.
- Kiểm tra các chức năng chính của customer và admin.

#### Kiến trúc frontend

Frontend được triển khai theo mô hình static hosting kết hợp CDN.

Luồng truy cập frontend:

```text
User / Admin
   ↓
Amazon CloudFront
   ↓
Amazon S3 Frontend Bucket
   ↓
Next.js static files
```

Khi người dùng truy cập website, request sẽ đi qua CloudFront. CloudFront lấy nội dung từ S3 Frontend Bucket và trả về cho người dùng.

Khi frontend cần lấy dữ liệu sản phẩm, đăng nhập, đặt hàng hoặc quản lý admin, frontend sẽ gọi API Gateway.

Luồng frontend gọi backend:

```text
Frontend Website
   ↓
Amazon API Gateway
   ↓
AWS Lambda
   ↓
Amazon DynamoDB
```

#### Di chuyển vào thư mục frontend

Mở terminal tại thư mục project:

```text
C:\Users\namda\ecommerce-aws
```

Sau đó di chuyển vào thư mục frontend:

```bash
cd frontend
```

Kiểm tra các file chính trong thư mục frontend:

```text
frontend/
├── src/
├── public/
├── package.json
├── next.config.ts
├── tsconfig.json
└── .env.local
```

Trong đó:

- `src/` chứa source code giao diện Next.js.
- `public/` chứa hình ảnh và static assets.
- `package.json` chứa scripts và dependencies.
- `next.config.ts` chứa cấu hình Next.js.
- `tsconfig.json` chứa cấu hình TypeScript.
- `.env.local` chứa biến môi trường dùng cho frontend.

#### Cài đặt dependencies cho frontend

Trong thư mục `frontend`, chạy lệnh:

```bash
npm install
```

Lệnh này sẽ cài các thư viện cần thiết để chạy và build frontend.

Sau khi cài đặt xong, kiểm tra thư mục sau đã được tạo:

```text
frontend/node_modules/
```

<img src="/images/5-Workshop/5.4-Frontend-deployment/frontend-npm.png" alt="Install Frontend Dependencies" style="max-width: 100%; height: auto;">

#### Cấu hình biến môi trường frontend

Frontend cần biết các thông tin backend như API Gateway URL, Cognito User Pool ID và Cognito App Client ID.

Tạo hoặc mở file:

```text
frontend/.env.local
```

Thêm nội dung sau:

```env
NEXT_PUBLIC_API_BASE_URL=https://89yap0gq5k.execute-api.ap-southeast-1.amazonaws.com/prod

NEXT_PUBLIC_AWS_REGION=ap-southeast-1

NEXT_PUBLIC_CUSTOMER_USER_POOL_ID=ap-southeast-1_LHSScZri7
NEXT_PUBLIC_CUSTOMER_USER_POOL_CLIENT_ID=1uijbf07brda1p130293v3prfv

NEXT_PUBLIC_ADMIN_USER_POOL_ID=ap-southeast-1_k57hCsM4z
NEXT_PUBLIC_ADMIN_USER_POOL_CLIENT_ID=1qc3ttu4q3e95pk7mlqsivr66e

NEXT_PUBLIC_PRODUCT_IMAGES_BASE_URL=https://dfjng6e5jz4mf.cloudfront.net/uploads
```

Lưu ý:

- Với Next.js, các biến môi trường dùng ở phía browser cần bắt đầu bằng `NEXT_PUBLIC_`.
- Không đưa secret thật vào file `.env.local`.
- Các giá trị này lấy từ tab **Outputs** của CloudFormation stack.
- Nếu API Gateway hoặc Cognito thay đổi, cần cập nhật lại file `.env.local` rồi build lại frontend.

<img src="/images/5-Workshop/5.4-Frontend-deployment/tao-env.png" alt="Frontend Environment Variables" style="max-width: 100%; height: auto;">

#### Kiểm tra package.json

Mở file:

```text
frontend/package.json
```

Kiểm tra phần `scripts` có các lệnh chính như sau:

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

Trong workshop này, chúng ta sẽ dùng:

```bash
npm run build
```

để build frontend production.

#### Chạy frontend local để kiểm tra

Trước khi deploy, nên chạy frontend local để kiểm tra giao diện và kết nối API.

Chạy lệnh:

```bash
npm run dev
```

Kết quả mong đợi:

```text
Local: http://localhost:3000
```

Mở trình duyệt:

```text
http://localhost:3000
```

Kiểm tra các trang chính:

- Trang chủ.
- Trang danh sách sản phẩm.
- Trang chi tiết sản phẩm.
- Trang giỏ hàng.
- Trang checkout.
- Trang đăng nhập customer.
- Trang đăng ký customer.
- Trang lịch sử đơn hàng.
- Trang admin login.
- Trang admin dashboard.
- Trang quản lý sản phẩm.
- Trang quản lý đơn hàng.
- Trang quản lý hoàn tiền.

<img src="/images/5-Workshop/5.4-Frontend-deployment/frontend-local.png" alt="Frontend Local Website" style="max-width: 100%; height: auto;">

#### Build frontend production

Sau khi kiểm tra local thành công, build frontend production:

```bash
npm run build
```

Kết quả mong đợi:

```text
Compiled successfully
```

Nếu project đang cấu hình static export, sau khi build thành công Next.js sẽ tạo thư mục output dùng để upload lên S3.

Thư mục output thường là:

```text
frontend/out/
```

<img src="/images/5-Workshop/5.4-Frontend-deployment/frontend-build.png" alt="Frontend Build Success" style="max-width: 100%; height: auto;">

#### Xử lý lỗi build thường gặp

##### Lỗi TypeScript trong thư mục .next

Nếu gặp lỗi liên quan đến:

```text
.next/dev/types/validator.ts
```

có thể do cache type của Next.js bị lỗi.

Cách xử lý:

```bash
rm -rf .next
npm run build
```

Trên Windows PowerShell có thể dùng:

```powershell
Remove-Item -Recurse -Force .next
npm run build
```

##### Lỗi thiếu biến môi trường

Nếu frontend gọi API sai hoặc không hiển thị dữ liệu, kiểm tra lại file:

```text
frontend/.env.local
```

Đảm bảo các biến môi trường có tiền tố:

```text
NEXT_PUBLIC_
```

##### Lỗi API bị CORS

Nếu browser báo lỗi CORS khi frontend gọi API Gateway, cần kiểm tra cấu hình CORS trong API Gateway hoặc Lambda response headers.

Các headers thường cần có:

```text
Access-Control-Allow-Origin
Access-Control-Allow-Headers
Access-Control-Allow-Methods
```

#### Kiểm tra thư mục output

Sau khi build thành công, kiểm tra thư mục:

```text
frontend/out/
```

Thư mục này chứa các file static như:

```text
out/
├── index.html
├── products/
├── account/
├── admin/
├── _next/
└── 404.html
```

Các file này sẽ được upload lên S3 Frontend Bucket.

#### Upload frontend lên Amazon S3

Sau khi build xong, upload toàn bộ nội dung trong thư mục `out` lên S3 Frontend Bucket.

Lệnh upload:

```bash
aws s3 sync out/ s3://ecommerce-aws-backend-frontendbucket-9wu7aid8okd8 --delete
```

Giải thích:

- `out/` là thư mục chứa static files sau khi build.
- `s3://ecommerce-aws-backend-frontendbucket-9wu7aid8okd8` là Frontend Bucket.
- `--delete` giúp xóa các file cũ trên S3 nếu local không còn tồn tại.

<img src="/images/5-Workshop/5.4-Frontend-deployment/s3-sync.png" alt="Upload Frontend to S3" style="max-width: 100%; height: auto;">

#### Kiểm tra file trong S3 bucket

Vào AWS Console:

```text
Amazon S3 → Buckets
```

Chọn Frontend Bucket:

```text
ecommerce-aws-backend-frontendbucket-9wu7aid8okd8
```

Kiểm tra các file frontend đã được upload, ví dụ:

```text
index.html
404.html
_next/
products/
admin/
account/
```

Nếu các file này đã xuất hiện trong bucket, nghĩa là quá trình upload frontend đã thành công.

<img src="/images/5-Workshop/5.4-Frontend-deployment/s3-frontend-files.png" alt="S3 Frontend Files" style="max-width: 100%; height: auto;">

#### Tạo CloudFront invalidation

Sau khi upload frontend lên S3, cần tạo CloudFront invalidation để CloudFront lấy phiên bản mới nhất.

Chạy lệnh:

```bash
aws cloudfront create-invalidation --distribution-id E3EAY8XN54SSDX --paths "/*"
```

Giải thích:

- `E3EAY8XN54SSDX` là CloudFront Distribution ID.
- `--paths "/*"` yêu cầu CloudFront làm mới toàn bộ cache.

Kết quả trả về sẽ có dạng:

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

#### Kiểm tra CloudFront invalidation

Vào AWS Console:

```text
CloudFront → Distributions
```

Chọn distribution:

```text
E3EAY8XN54SSDX
```

Mở tab:

```text
Invalidations
```

Trạng thái mong đợi:

```text
Completed
```

Nếu trạng thái vẫn là:

```text
InProgress
```

hãy đợi thêm vài phút.

<img src="/images/5-Workshop/5.4-Frontend-deployment/done.png" alt="CloudFront Invalidation Complete" style="max-width: 100%; height: auto;">

#### Mở website bằng CloudFront URL

Sau khi invalidation hoàn tất, mở website bằng CloudFront URL:

```text
https://dfjng6e5jz4mf.cloudfront.net
```

Nếu website hiển thị đúng giao diện, nghĩa là frontend đã được deploy thành công.

<img src="/images/5-Workshop/5.4-Frontend-deployment/cloudfront-website.png" alt="CloudFront Website" style="max-width: 100%; height: auto;">

#### Kiểm tra các trang customer

Truy cập và kiểm tra các trang customer:

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

Các chức năng cần kiểm tra:

- Trang chủ hiển thị đúng.
- Danh sách sản phẩm hiển thị từ API.
- Trang chi tiết sản phẩm hoạt động.
- Có thể thêm sản phẩm vào giỏ hàng.
- Trang checkout hiển thị đúng.
- Customer có thể đăng ký.
- Customer có thể đăng nhập.
- Customer có thể xem lịch sử đơn hàng.

#### Kiểm tra các trang admin

Truy cập và kiểm tra các trang admin:

```text
https://dfjng6e5jz4mf.cloudfront.net/admin/login
https://dfjng6e5jz4mf.cloudfront.net/admin
https://dfjng6e5jz4mf.cloudfront.net/admin/products
https://dfjng6e5jz4mf.cloudfront.net/admin/orders
https://dfjng6e5jz4mf.cloudfront.net/admin/refunds
```

Các chức năng cần kiểm tra:

- Admin có thể đăng nhập.
- Admin dashboard hiển thị đúng.
- Admin có thể xem danh sách sản phẩm.
- Admin có thể tạo sản phẩm mới.
- Admin có thể upload hình ảnh sản phẩm.
- Admin có thể xem đơn hàng.
- Admin có thể quản lý hoàn tiền.

<img src="/images/5-Workshop/5.4-Frontend-deployment/admin-page.png" alt="Admin Page" style="max-width: 100%; height: auto;">

#### Kiểm tra frontend gọi API Gateway

Mở Developer Tools trên trình duyệt:

```text
F12 → Network
```

Sau đó reload website và kiểm tra các request gọi API.

Các API thường thấy:

```text
/products
/product
/cart
/checkout
/account/orders
/admin/products
/admin/orders
```

Nếu status code là:

```text
200
```

nghĩa là frontend gọi API thành công.

Nếu gặp lỗi:

```text
401 Unauthorized
```

cần kiểm tra lại token đăng nhập hoặc Cognito configuration.

Nếu gặp lỗi:

```text
403 Forbidden
```

cần kiểm tra quyền hoặc authorizer.

Nếu gặp lỗi:

```text
500 Internal Server Error
```

cần mở CloudWatch Logs của Lambda để debug.

#### Một số lỗi thường gặp khi deploy frontend

##### Website vẫn hiển thị phiên bản cũ

Nguyên nhân thường là CloudFront vẫn cache bản cũ.

Cách xử lý:

```bash
aws cloudfront create-invalidation --distribution-id E3EAY8XN54SSDX --paths "/*"
```

##### Trang con bị lỗi khi refresh

Ví dụ refresh tại:

```text
/admin/products
```

bị lỗi 403 hoặc 404.

Nguyên nhân là S3 không tự hiểu route của Next.js static export.

Cách xử lý:

- Kiểm tra CloudFront Function hoặc error response rewrite.
- Đảm bảo CloudFront rewrite route về `index.html` hoặc đúng file HTML tương ứng.
- Kiểm tra cấu hình static export của Next.js.

##### Frontend gọi sai API URL

Nếu frontend gọi sai backend URL, kiểm tra lại:

```text
frontend/.env.local
```

Sau khi sửa `.env.local`, cần build lại:

```bash
npm run build
```

rồi upload lại lên S3.

##### Ảnh sản phẩm không hiển thị

Kiểm tra các nguyên nhân sau:

- Product image URL trong DynamoDB có đúng không.
- S3 Product Images Bucket có chứa file ảnh không.
- CloudFront có phân phối đúng path `/uploads` không.
- CORS của S3 đã được cấu hình đúng chưa.

##### Admin bị đăng xuất hoặc báo hết phiên

Nguyên nhân có thể do token Cognito hết hạn hoặc cấu hình User Pool Client chưa đúng.

Cách xử lý:

- Đăng nhập lại admin.
- Kiểm tra Admin User Pool ID.
- Kiểm tra Admin User Pool Client ID.
- Kiểm tra frontend env variables.

#### Kết quả đạt được

Sau khi hoàn thành phần này, bạn đã triển khai thành công frontend của dự án ecommerce lên AWS.

Các kết quả đạt được gồm:

- Next.js frontend đã được build thành static files.
- Static files đã được upload lên Amazon S3 Frontend Bucket.
- CloudFront đã phân phối website ra Internet.
- CloudFront invalidation đã được tạo để cập nhật phiên bản mới.
- Customer có thể truy cập website bán hàng.
- Admin có thể truy cập trang quản trị.
- Frontend có thể gọi API Gateway.
- Frontend đã kết nối với Cognito để đăng nhập customer và admin.
- Website đã sẵn sàng để kiểm thử các chức năng sản phẩm, giỏ hàng, đặt hàng và thanh toán.

Ở phần tiếp theo, chúng ta sẽ cấu hình và kiểm thử authentication bằng Amazon Cognito cho customer và admin.
