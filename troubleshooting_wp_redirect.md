# KHẮC PHỤC LỖI CHUYỂN HƯỚNG TÊN MIỀN CŨ (WORDPRESS REDIRECT ISSUE)

Tài liệu này hướng dẫn xử lý lỗi khi truy cập website WordPress mới nhưng trình duyệt lại tự động chuyển hướng về tên miền cũ hoặc báo lỗi bảo mật đỏ.

## 1. Phân tích nguyên nhân (Root Cause)
Mã nguồn WordPress lưu trữ cấu hình tên miền (Site URL và Home URL) trực tiếp trong Cơ sở dữ liệu (Database). 
* **Vấn đề:** Khi nạp file backup `.sql` từ môi trường cũ (ví dụ: `linhlt.id.vn`), các giá trị này vẫn mang tên miền cũ.
* **Hệ quả:** Khi truy cập tên miền mới (`wp.diemly.vietnix.tech`), mã nguồn WordPress bắt trình duyệt Redirect về tên miền cũ. Nếu tên miền cũ không tồn tại hoặc không có SSL trên máy chủ mới, trình duyệt sẽ báo lỗi **NET::ERR_CERT_AUTHORITY_INVALID**.

## 2. Các bước khắc phục (Troubleshooting)

### Bước 1: Truy cập quản trị Database
1. Đăng nhập vào **cPanel**.
2. Tìm mục **Databases** -> Chọn **phpMyAdmin**.
3. Ở cột bên trái, chọn đúng tên cơ sở dữ liệu của bạn: `wpdiemly_wp`.

### Bước 2: Chỉnh sửa bảng Options
1. Tìm bảng có tên kết thúc bằng `_options` (Ví dụ: `Sa3QlZ_options`).
2. Quan sát cột `option_name` và tìm 2 dòng:
   * **siteurl** (thường ở dòng 1)
   * **home** (thường ở dòng 2)
3. **Thao tác:** * Nhấp đúp chuột vào giá trị cũ (ví dụ: `https://linhlt.id.vn`) ở cột `option_value`.
   * Xóa giá trị cũ và nhập tên miền mới chính xác: 
     👉 `https://wp.diemly.vietnix.tech`
   * Nhấn **Enter** để lưu.

### Bước 3: Kiểm tra lại
* Mở trình duyệt ở chế độ **Ẩn danh (Incognito)** để tránh bộ nhớ đệm (cache) cũ.
* Truy cập lại tên miền mới để kiểm tra kết quả.