## GIAI ĐOẠN 2: CẤU HÌNH TÊN MIỀN VÀ KHỞI TẠO CƠ SỞ DỮ LIỆU (Domain & Database Provisioning)  - môi trường HOSTING

### Bước 2.1: Khai báo tên miền Laravel 
Đã cài sẵn tên miền WordPress (`wp.diemly.vietnix.tech`) vào thư mục gốc tên là `public_html`. Việc của bạn bây giờ là tạo thêm một "căn phòng" mới cho Laravel.

**1. Đăng nhập vào cPanel:**
* **Thao tác:** truy cập vào đường link: `https://host68.vietnix.vn:2083/`
* **Nhập thông tin:** * Username: `wpdiemly`
  * Password: `bA7yMsU3U4dVKf`
* Bấm **Log in**.

**2. Tìm công cụ quản lý Domain:**
* **Thao tác:** Khi vào được màn hình chính, bạn sẽ thấy rất nhiều biểu tượng. Để tìm nhanh, hãy gõ chữ **Domains** vào ô tìm kiếm (Search) ở ngay trên cùng, sau đó click vào biểu tượng Domains.

**3. Khai báo tên miền mới:**
* **Thao tác:** Nhấp vào nút màu xanh **Create A New Domain** ở góc phải.
* **Điền thông tin thật cẩn thận theo các bước sau:**
  * **Domain:** Gõ chính xác `laravel.diemly.vietnix.tech`
  * 🚨 **Document Root (Cực kỳ quan trọng):** Ngay bên dưới ô Domain, bạn sẽ thấy một ô vuông có chữ *Share document root...*. Bạn **BẮT BUỘC PHẢI BỎ DẤU TÍCH** ở ô này. (Nếu để nguyên, mã nguồn Laravel sẽ bị trộn lẫn vào phòng của WordPress, gây hỏng cả 2 web).
  * Sau khi bỏ dấu tích, hệ thống sẽ tự động điền đường dẫn là `laravel.diemly.vietnix.tech` vào ô phía dưới. Bạn hãy click chuột vào cuối dòng chữ đó và gõ thêm chữ `/public` vào để đường dẫn trở thành:
    👉 `/laravel.diemly.vietnix.tech/public`
  * **Hoàn tất:** Bấm nút **Submit** và đợi vài giây để hệ thống tạo phòng.

**Tổng kết các thông số quan trọng:**
* **Domain:** Điền đúng tên miền `laravel.diemly.vietnix.tech`.
* **Share document root:** Bỏ dấu tích ở ô này. Điều này đảm bảo code Laravel sẽ có một “căn phòng riêng” độc lập, không bị trộn lẫn mã nguồn với WordPress.
* **Document Root:** Thêm đuôi `/public` vào chính xác. Đây là yếu tố Laravel có thể chạy được và bảo mật các file cấu hình hệ thống.
* **Giải thích:** Laravel có tính bảo mật rất cao. Nó không cho phép người ngoài truy cập vào thư mục chứa code, mà chỉ được phép đi qua "cửa chính" là thư mục `public`. Việc thêm `/public` giúp khóa chặt các file hệ thống lại.

---

### Bước 2.2: Tạo Cơ sở dữ liệu (Tạo kho chứa dữ liệu)
Bây giờ web đã có chỗ để code, nhưng chưa có “nơi” để chứa bài viết, sản phẩm, tài khoản người dùng.

**1. Tìm công cụ MySQL:**
* **Thao tác:** Bấm vào logo cPanel để quay về trang chủ. Kéo xuống tìm khu vực “DATABASES” và click vào biểu tượng **MySQL® Databases**.

**2. Tạo Database:**
* **Thao tác:** Ở mục Create New Database:
  * Ô *New Database*: Bạn gõ chữ `wp` rồi bấm nút **Create Database**.
  * Bấm nút **Go Back** (Quay lại) để tạo tiếp.
  * Lặp lại bước trên, gõ chữ `laravel` rồi bấm **Create Database**, sau đó lại **Go Back**.

**Lưu ý:** Tên kho thực tế của bạn sẽ được tự động gắn thêm tiền tố tài khoản. Note 2 database:
1. `wpdiemly_wp`
2. `wpdiemly_laravel`

**Giải thích dễ hiểu:** * **Bản chất của cPanel (Shared Hosting):** Khác với máy chủ ảo VPS là “nhà riêng” của bạn (bạn muốn đặt tên DB là gì cũng được, không ai cấm), cPanel là một “khu chung cư” có rất nhiều tài khoản cùng sử dụng chung một máy chủ vật lý.
* **Quy tắc chống trùng lặp (Namespace):** Giả sử bạn muốn tạo một Database tên là `laravel`, và một người khác trên cùng máy chủ cPanel này cũng muốn tạo một Database tên là `laravel`. Để hệ thống không bị nhầm lẫn và ghi đè dữ liệu lên nhau, cPanel sinh ra luật: Bắt buộc gắn tên đăng nhập (Username) làm tiền tố.
* **Kết quả:** Vì tài khoản đăng nhập cPanel của tôi là `wpdiemly`, nên bất kỳ database nào tôi tạo ra cũng sẽ bị ép dính chặt tiền tố `wpdiemly_` ở đằng trước. Của tôi sẽ là `wpdiemly_laravel`, của người khác sẽ là `nguoikhac_laravel`. Không ai đụng chạm tới ai cả.

**3. Tạo User (Người giữ key database):**
* **Thao tác:** Tìm phần Add New User:
  * **Username:** Gõ `admin_db`
  * **Password:** Gõ `Ly@2026!vietnix`. Gõ 2 lần để xác nhận.
  * Nhấn nút **Create User**, sau đó bấm **Go Back**.

**4. Cấp quyền cho User vào quản lý Database:**
* **Thao tác:** Cuộn chuột xuống gần cuối, tìm phần Add User To Database.
  * Ô **User**: Chọn đúng tên `wpdiemly_admin_db`
  * Ô **Database**: Chọn `wpdiemly_wp` (Kho WordPress)
  * Bấm nút **Add**.
  * Một trang mới hiện ra, bạn Tích vào ô kiểm **ALL PRIVILEGES** (Cấp toàn bộ quyền: Xóa, Sửa, Thêm dữ liệu...).
  * Bấm **Make Changes**, sau đó bấm **Go Back**.
* **Làm lại lần 2 cho Laravel:**
  * Vẫn ở phần Add User To Database.
  * Ô **User**: Chọn lại `wpdiemly_admin_db`
  * Ô **Database**: Chọn `wpdiemly_laravel` (Kho Laravel)
  * Bấm **Add** -> Tích **ALL PRIVILEGES** -> **Make Changes**.

---

### NOTE: BẢNG TỔNG HỢP THÔNG TIN DATABASE

| Hạng mục | Thông tin chi tiết | Ghi chú |
| :--- | :--- | :--- |
| **Tên Database 1** | `wpdiemly_wp` | Dùng cho web WordPress (Century Auto) |
| **Tên Database 2** | `wpdiemly_laravel` | Dùng cho web Laravel (Coza Store) |
| **User quản trị** | `wpdiemly_admin_db` | Dùng chung cho cả 2 Database |
| **Mật khẩu (Pass)** | `Ly@2026!vietnix` | |
| **Database Host** | `localhost` | Luôn là localhost trên cPanel |

