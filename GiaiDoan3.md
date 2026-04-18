## GIAI ĐOẠN 3: TRIỂN KHAI DỮ LIỆU LÊN HOSTING VÀ ĐỒNG BỘ DỮ LIỆU

**Mục đích:** Đưa 4 file ở giai đoạn 1 lên cPanel và hiệu chỉnh tham số cấu hình để hệ thống vận hành trên môi trường mới

### Bước 3.1: Triển khai mã nguồn (Source Code Deployment)
Chúng ta sẽ đưa các file `.zip` lên các thư mục gốc (Root Directory) tương ứng trên cPanel.

* **Truy cập trình quản lý tệp:** Từ trang chủ cPanel, tìm module **Files** -> Chọn **File Manager**.

**Triển khai WordPress:**
* Tại cột bên trái, click vào thư mục `public_html`.
* Chọn **Upload** trên thanh công cụ -> Chọn file `source_wq_vps.zip` từ máy Mac.
* Khi thanh tiến trình hiện màu xanh (100%), quay lại thư mục, chuột phải vào file zip -> chọn **Extract** (Giải nén) - giữ nguyên thư mục này trong thư mục `public_html`.

🚨 **Lưu ý:** Đem tất cả các thư mục con trong `source_wq` sang ra thư mục ngoài là `public_html`; xoá đi những file không cần thiết.

**Triển khai Laravel:**
* Tại cột bên trái, click vào thư mục `laravel.diemly.vietnix.tech`.
* Thực hiện **Upload** và **Extract** file `laravel_source_vps.zip` tương tự như trên. Đảm bảo thư mục `public` nằm ngay bên trong thư mục tên miền này.

---

### Bước 3.2: Khởi tạo dữ liệu (Database Import)
Đây là bước đưa của website từ VPS sang cPanel thông qua công cụ quản trị giao diện phpMyAdmin.

* Quay lại trang chủ cPanel, tìm module **Databases** -> Chọn **phpMyAdmin**.

**Nhập dữ liệu WordPress:**
* Ở cột bên trái, click chọn đúng database `wpdiemly_wp`.
* Chọn tab **Import** ở thanh menu phía trên.
* Tại mục **File to import**, chọn file `db_wp_vps.sql`.
* Kéo xuống cuối cùng và nhấn **Import** (hoặc **Go**). Đợi hệ thống báo dòng chữ xanh "Import has been successfully finished".

**Nhập dữ liệu Laravel:**
* Chọn database `wpdiemly_laravel` ở cột trái và thực hiện các bước Import tương tự với file `db_laravel_vps.sql`.

---

### Bước 3.3: Hiệu chỉnh tham số kết nối (Connection Configuration)
Đây là bước quan trọng nhất. Cần thay đổi thông tin Database cũ (từ VPS) thành thông tin Database mới (trên cPanel) mà chúng ta đã lưu ở bảng thống kê Giai đoạn 2.

**Cấu hình cho WordPress:**
* Quay lại **File Manager** -> vào `public_html`.
* Chuột phải vào file `wp-config.php` -> Chọn **Edit**.
* Tìm các dòng sau và cập nhật giá trị mới:
    * `DB_NAME`: thay thành `'wpdiemly_wp'`
    * `DB_USER`: thay thành `'wpdiemly_admin_db'`
    * `DB_PASSWORD`: thay thành `'Ly@2026!vietnix'`
* Nhấn **Save Changes**.

**Cấu hình cho Laravel:**
* Vào thư mục `laravel.diemly.vietnix.tech`.
* Chuột phải vào file `.env` -> Chọn **Edit**.

**Lưu ý:**
* **Hiện file ẩn:** Trong bức ảnh bạn chụp, mình không thấy file `.env`. Đây là do cPanel đang ẩn các file có dấu chấm ở đầu.
* Nhìn lên góc phải trên cùng, bấm vào nút ⚙️ **Settings** (Cài đặt).
* Một bảng nhỏ hiện ra, bạn tích chọn vào ô **Show Hidden Files (dotfiles)**.
* Bấm **Save**. Lúc này file `.env` sẽ xuất hiện ngay lập tức!

* Tìm và cập nhật các biến môi trường sau:
    ```env
    DB_DATABASE=wpdiemly_laravel
    DB_USERNAME=wpdiemly_admin_db
    DB_PASSWORD=Ly@2026!vietnix
    ```
* Nhấn **Save Changes**.
