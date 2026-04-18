# GHI CHÚ BÀI LAB: CHUYỂN HỆ THỐNG TỪ VPS SANG CPANEL (MIGRATION)

**Yêu cầu:**
* Upload 2 source code Wordpress và Laravel ở Topic 4 vào cPanel với 2 domain tương ứng đã cấp trước đó.
* Chủ động thêm domain còn lại vào hosting và trỏ domain về IP Hosting với file `hosts` (Local DNS).
* Upload lại cert SSL từ VPS đã tạo trước đó để thiết lập HTTPS.

**Thông tin truy cập cPanel:**
* URL: `https://host68.vietnix.vn:2083/`
* Tài khoản: `wpdiemly`
* Mật khẩu: `bA7yMsU3U4dVKf`

---

## 1. Tổng quan yêu cầu
Di chuyển (Migrate) 2 website (**WordPress - Century Auto** và **Laravel - Coza Store**) từ môi trường VPS sang môi trường Shared Hosting sử dụng bảng điều khiển cPanel. 

## 2. Mục tiêu đạt được
* Hai website truy cập bình thường trên nền tảng cPanel.
* File `hosts` trên máy Mac được cập nhật để phân giải 2 tên miền về IP mới của cPanel (thay vì IP của VPS cũ).
* Website có kết nối an toàn (HTTPS) bằng cách tái sử dụng chứng chỉ SSL đã được cấp phát trên VPS.

## 3. Xác định các thư mục & file từ VPS cần di chuyển
Trước khi bắt đầu, chúng ta cần xác định rõ mình phải lấy những tài nguyên nào trên VPS để "mang về" nhà mới:

* **Mã nguồn WordPress:** Nằm ở `/var/www/source_wq/`
* **Mã nguồn Laravel:** Nằm ở `/var/www/laravel_source/`
* **Cơ sở dữ liệu WordPress:** Tên DB là `wordpress_db`
* **Cơ sở dữ liệu Laravel:** Tên DB là `laravel_db`
* **Chứng chỉ SSL:** Nằm ở `/etc/letsencrypt/live/wp.diemly...` và `/laravel.diemly...`

Dưới đây là các bước thực hành trên Terminal để xác định chính xác các thành phần này:

### 3.1 Lệnh kiểm tra vị trí Source Code (Mã nguồn)
Theo mặc định của máy chủ Nginx/Apache, mã nguồn web thường được đặt ở thư mục `/var/www/`. Để xác nhận 2 thư mục code có nằm ở đây không, hãy dùng lệnh sau:

```bash
ls -la /var/www/
````

**Giải thích lệnh:**

  * `ls` (list): Lệnh yêu cầu máy chủ liệt kê danh sách các tệp tin và thư mục.
  * `-la`: Tham số bổ sung giúp liệt kê dưới dạng danh sách chi tiết (long format) và hiển thị cả các file ẩn (all).
  * `/var/www/`: Đường dẫn đến nơi cần kiểm tra.

**Kết quả:** Trên màn hình sẽ in ra một danh sách. Nếu nhìn thấy hai thư mục `source_wq` và `laravel_source` ở trong danh sách này, nghĩa là code đã nằm đúng vị trí.

### 3.2 Lệnh kiểm tra tên Database (Cơ sở dữ liệu)

Đôi khi trong quá trình làm việc, chúng ta đặt tên Database khác đi một chút (ví dụ `wordpress_db` hoặc `wp_db`). Để kiểm tra chính xác tên 2 file SQL cần xuất, chạy lệnh sau:

```bash
mysql -u root -p -e "SHOW DATABASES;"
```

**Giải thích lệnh:**

  * `mysql`: Gọi phần mềm quản trị cơ sở dữ liệu.
  * `-u root -p`: Đăng nhập bằng quyền cao nhất (root) và yêu cầu nhập mật khẩu (password).
  * `-e "SHOW DATABASES;"`: Tham số `-e` (execute) yêu cầu MySQL chạy ngay lập tức câu lệnh `SHOW DATABASES;` rồi thoát ra ngay, thay vì phải đi hẳn vào giao diện MySQL CLI.

**Kết quả:** Hệ thống sẽ in ra một bảng danh sách các Database. Từ bảng này, ta xác định được chính xác tên Database của WordPress là `wordpress_db` và Laravel là `laravel_db`.

### 3.3 Lệnh kiểm tra vị trí Chứng chỉ SSL

Chúng ta đã dùng công cụ Certbot để xin cấp SSL miễn phí. Công cụ này có một quy tắc là luôn lưu trữ chìa khóa ở thư mục `/etc/letsencrypt/live/`. Dùng lệnh này để kiểm tra xem chìa khóa có ở đó không:

```bash
ls -l /etc/letsencrypt/live/
```

**Giải thích lệnh:** Lệnh liệt kê (`ls`) danh sách các thư mục con nằm trong "két sắt" của Let's Encrypt (`/etc/letsencrypt/live/`).

**Kết quả:** Màn hình xuất hiện danh sách các thư mục được đặt tên theo chính tên miền của bạn, cụ thể là: `wp.diemly.vietnix.tech` và `laravel.diemly.vietnix.tech`. Trong các thư mục này chứa file `cert.pem` và `privkey.pem` mà chúng ta sẽ dùng để thiết lập cấu hình SSL ở bước cuối.

