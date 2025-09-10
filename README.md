 ChicWear – Website mua sắm thời trang

ChicWear là hệ thống thương mại điện tử thời trang với đầy đủ chức năng đặt hàng, giỏ hàng, thanh toán VNPay/MoMo/ZaloPay/PayPal, đánh giá, khuyến mãi, theo dõi đơn hàng và bộ màn hình quản trị. Mục tiêu là triển khai sản phẩm vào thực tiễn, đáp ứng ổn định 300–400 người dùng đồng thời, thời gian phản hồi dưới 5 giây và đảm bảo bảo mật dữ liệu.

---

Mục lục

- [Tổng quan dự án](#tổng-quan-dự-án)
- [Tính năng chính](#tính-năng-chính)
- [Kiến trúc hệ thống](#kiến-trúc-hệ-thống)
- [Cài đặt và chạy dự án](#cài-đặt-và-chạy-dự-án)
- [Cấu hình môi trường](#cấu-hình-môi-trường)
- [Thiết kế CSDL](#thiết-kế-csdl)
- [API chính](#api-chính)
- [Thanh toán trực tuyến](#thanh-toán-trực-tuyến)
- [Bảo mật](#bảo-mật)
- [Hiệu năng và khả năng mở rộng](#hiệu-năng-và-khả-năng-mở-rộng)
- [Kiểm thử và chất lượng](#kiểm-thử-và-chất-lượng)
- [Giám sát, logging và sao lưu](#giám-sát-logging-và-sao-lưu)
- [Triển khai sản xuất](#triển-khai-sản-xuất)
- [I18n, A11y, SEO](#i18n-a11y-seo)
- [Lộ trình phát triển](#lộ-trình-phát-triển)
- [Đóng góp và quy tắc code](#đóng-góp-và-quy-tắc-code)
- [Giấy phép](#giấy-phép)

---

Tổng quan dự án

- Tên: ChicWear
- Mục tiêu: Xây dựng website bán hàng thời trang trực tuyến thân thiện, an toàn, triển khai thực tế cho người dùng tại Việt Nam.
- Đối tượng sử dụng: Khách vãng lai, khách hàng đã đăng ký, quản trị viên.
- Công nghệ: ReactJS (FE), Laravel (BE), MySQL (DB), RESTful API, Redis (cache), Nginx (reverse proxy), Docker.
- Phi chức năng:
  - Hiệu năng: Thời gian xử lý < 5s cho 95% request.
  - Tải: 300–400 người dùng đồng thời.
  - Bảo mật: Mã hóa mật khẩu, bảo vệ thông tin thanh toán, phân quyền chặt chẽ.
  - Khả dụng: Hạn chế downtime, có chiến lược sao lưu/khôi phục.

---
Tính năng chính

Người dùng Internet
- Xem sản phẩm, tìm kiếm, đăng ký tài khoản.

Khách hàng
- Đăng nhập/đăng xuất, quản lý giỏ hàng, đặt hàng, thanh toán, theo dõi đơn, đánh giá sản phẩm, danh sách yêu thích, quản lý hồ sơ.

Quản trị viên
- Quản lý tài khoản, danh mục, sản phẩm, đơn hàng, khuyến mãi, thống kê doanh thu, nhật ký hoạt động.

---

 Kiến trúc hệ thống

- Frontend: ReactJS SPA, Redux/Zustand, Router.
- Backend: Laravel, kiến trúc service/repository, policy/guard.
- Database: MySQL 8.
- Cache/Queue: Redis.
- Proxy: Nginx.
- Containerization: Docker Compose.

---

Cài đặt và chạy dự án

Yêu cầu hệ thống
- Node.js 18+
- PHP 8.2+
- Composer 2.x
- MySQL 8.x
- Redis 6.x
- Docker 24+ (khuyến nghị)

 Chạy nhanh bằng Docker
```bash
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env
docker compose up -d --build
docker compose exec backend php artisan migrate --seed
```
Truy cập: http://localhost

---

 Cấu hình môi trường

 Backend (.env)
```env
APP_NAME=ChicWear
APP_ENV=local
APP_KEY=base64:...
APP_URL=http://localhost

DB_CONNECTION=mysql
DB_HOST=database
DB_PORT=3306
DB_DATABASE=chicwear
DB_USERNAME=chicwear
DB_PASSWORD=chicwear

CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis
REDIS_HOST=redis
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=you@gmail.com
MAIL_PASSWORD=app_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@chicwear.vn
MAIL_FROM_NAME="ChicWear"

PAYMENT_VNPAY_TMNCODE=...
PAYMENT_VNPAY_HASHSECRET=...
PAYMENT_VNPAY_RETURNURL=https://yourdomain.vn/payment/vnpay/return
```

 Frontend (.env)
```env
VITE_APP_NAME=ChicWear
VITE_API_BASE_URL=http://localhost/api
VITE_ENABLE_I18N=true
```

---

 Thiết kế CSDL
- NgườiDùng, DanhMuc, SảnPhẩm, BiếnThểSảnPhẩm, GiỏHàng, ĐơnHàng, ThanhToán, ĐánhGiá, KhuyếnMãi, NhậtKýAdmin, TheoDõiĐơnHàng, DanhSáchYêuThích.

---

 API chính
- Auth: `/auth/register`, `/auth/login`, `/auth/logout`
- Sản phẩm: `/products`, `/products/{id}`
- Giỏ hàng: `/cart`
- Đơn hàng: `/orders`
- Thanh toán: `/payments/{gateway}`
- Admin: `/admin/*`

---

 Thanh toán trực tuyến
- Tích hợp VNPay, MoMo, ZaloPay, PayPal.
- Xác thực checksum/HMAC.
- Callback idempotent.

---

 Bảo mật
- Mã hóa mật khẩu (bcrypt/argon2).
- JWT/Session cookie HttpOnly.
- Rate limit, CAPTCHA.
- RBAC, CSRF, CSP.

---

 Hiệu năng và khả năng mở rộng
- Cache, index DB, pagination, CDN, queue, horizontal scaling.

---

 Kiểm thử và chất lượng
- Unit test, feature test, E2E test.
- Static analysis, linting.

---

 Giám sát, logging và sao lưu
- Monolog, Sentry, Prometheus, Grafana.
- Backup DB hàng ngày.

---

 Triển khai sản xuất
- Nginx reverse proxy.
- CI/CD pipeline.
- TLS, domain, backup, monitoring.

---

 I18n, A11y, SEO
- Đa ngôn ngữ, hỗ trợ truy cập, tối ưu SEO.

---

 Lộ trình phát triển
- Ngắn hạn: gợi ý tìm kiếm, voucher động.
- Trung hạn: tích hợp vận chuyển, CMS.
- Dài hạn: microservices, event-driven.

---

 Đóng góp và quy tắc code
- Git flow, Conventional Commits, code style PSR-12 & ESLint.

---

 Giấy phép
- Dự án thuộc nhóm phát triển học phần Công cụ & Phương pháp Thiết kế – Quản lý (Phần mềm) – Đại học Duy Tân.
