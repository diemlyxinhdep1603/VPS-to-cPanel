## GIAI ĐOẠN 1: ĐÓNG GÓI VÀ LẤY DỮ LIỆU TỪ VPS VỀ MÁY LOCAL

### Bước 1.1: Nén Code (Gói đồ đạc vào thùng Zip)
**1. Đăng nhập vào VPS:**
Thao tác: 
```bash
ssh root@221.132.21.142
```

**2. Đi đến thư mục chứa mã nguồn:**
Thao tác: 
```bash
cd /var/www/
```

**3. Nén mã nguồn WordPress:**
Thao tác: 
```bash
zip -r source_wq_vps.zip source_wq/
```
Giải thích: `zip` là phần mềm nén. `-r` (recursive) nghĩa là "nén tất cả mọi thứ bên trong, không bỏ sót cái gì".

**4. Nén mã nguồn Laravel:**
Thao tác: 
```bash
zip -r laravel_source_vps.zip laravel_source/
```

### Bước 1.2: Xuất Database 
Vẫn đang ở trong VPS, chúng ta sẽ chuyển dữ liệu trong MySQL ra thành file văn bản.

**1. Xuất Database WordPress:**
Thao tác: 
```bash
mysqldump -u root -p wordpress_db > /root/db_wp_vps.sql
```

Giải thích chi tiết lệnh:
* `mysqldump`: Công cụ xuất dữ liệu.
* `-u root`: Đăng nhập với tư cách user cao nhất là root.
* `-p`: Yêu cầu hệ thống hỏi mật khẩu.
* `wordpress_db`: Tên cái kho dữ liệu bạn muốn xuất.
* `>`: Dấu mũi tên này có nghĩa là "Đẩy toàn bộ dữ liệu vào file...".
* `/root/db_wp_vps.sql`: Tên và vị trí file mà bạn muốn tạo ra (mình đang lưu nó ở thư mục gốc của VPS cho dễ tìm).

Kết quả: Nếu thành công, hệ thống im lặng và không báo gì cả, chỉ hiện lại dòng `root@training-diemly:~#`. 

**2. Xuất Database Laravel:**
Thao tác: 
```bash
mysqldump -u root -p laravel_db > /root/db_laravel_vps.sql
```

### Bước 1.3: Kiểm tra trước khi tải 
Để chắc chắn 4 file đã được tạo ra đàng hoàng và không bị lỗi (file trống 0 byte), chạy 2 lệnh sau:
```bash
ls -lh /var/www/*.zip
ls -lh /root/*.sql
```

Hiện tượng: Nếu thấy 4 dòng tên file (`source_wq_vps.zip`, `laravel_source_vps.zip`, `db_wp_vps.sql`, `db_laravel_vps.sql`) kèm theo dung lượng của chúng (ví dụ vài chục M - Megabyte) thì ok.

### Bước 1.4: Tải các file đã xử lý từ VPS về máy Local
🚨 **LƯU Ý ĐÂY LÀ BƯỚC HAY NHẦM LẪN NHẤT:** Bạn không thể tải file về Local nếu bạn vẫn đang gõ lệnh ở “bên trong” VPS. Bạn phải dùng Local để “đi vào” VPS kéo file ra.

**1. Mở Tab Terminal Mới trên Mac:**
Hiện tượng: Dòng chữ đầu tiên không còn là `root@training-diemly` nữa, mà là tên máy tính của bạn 

**2. Kéo file về (Dùng lệnh SCP - Secure Copy):**
Thao tác: 
```bash
scp root@221.132.21.142:/var/www/source_wq_vps.zip ~/Downloads/
```
Giải thích lệnh: `scp` (copy bảo mật) từ địa chỉ `root@...` (nơi chứa file trên VPS) mang về `~/Downloads/` (Thư mục Downloads trên máy của bạn).

Hiện tượng: Nó sẽ hỏi mật khẩu VPS. Sau khi nhập pass, bạn sẽ thấy một thanh tiến trình chạy phần trăm 100%.

**3. Tải 3 file còn lại:**
Làm lặp lại với 3 lệnh tiếp theo (mỗi lệnh dán vào, đợi chạy 100% rồi mới dán lệnh tiếp theo):
```bash
scp root@221.132.21.142:/var/www/laravel_source_vps.zip ~/Downloads/
```

```bash
scp root@221.132.21.142:/root/db_wp_vps.sql ~/Downloads/
```

```bash
scp root@221.132.21.142:/root/db_laravel_vps.sql ~/Downloads/
```

✅ **KIỂM TRA THÀNH QUẢ CUỐI CÙNG:** Thấy 4 file nằm ở download.

