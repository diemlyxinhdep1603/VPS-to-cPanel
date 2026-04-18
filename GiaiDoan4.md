## GIAI ĐOẠN 4: ĐỊNH TUYẾN TÊN MIỀN NỘI BỘ VÀ TÍCH HỢP SSL (Local DNS Routing & SSL Integration)

**Mục đích:** Cập nhật phân giải tên miền (DNS) trên máy tính cá nhân để trỏ về IP của máy chủ Hosting mới, đồng thời di chuyển chứng chỉ bảo mật (SSL) từ VPS sang cPanel để đảm bảo kết nối HTTPS (ổ khóa xanh).

### Bước 4.1: Định tuyến tên miền về cPanel (Sửa file hosts trên Mac)
Bản chất hiện tại trên mạng Internet, 2 tên miền của bạn vẫn đang trỏ về IP của VPS cũ. Chúng ta phải dùng file hosts để "ép" riêng máy Local khi gõ tên miền thì phải chạy vào cPanel.

**1. Lấy địa chỉ IP của cPanel:**
* Mở một Tab Terminal mới trên Mac (Tab Local, không SSH vào VPS).
```bash
ping -c 1 host68.vietnix.vn
```
* Bạn sẽ thấy một dòng kết quả có chứa dãy số IP (Ví dụ: `103.130.xxx.xxx`). Hãy copy hoặc ghi nhớ dãy số này.

**2. Sửa file hosts:**
* Vẫn ở Terminal của Mac, gõ lệnh:
```bash
sudo nano /etc/hosts
```
* Dùng phím mũi tên di chuyển con trỏ nhấp nháy xuống dòng dưới cùng. Thêm 2 dòng sau (Nhớ thay chữ `IP_CPANEL` bằng dãy số IP bạn vừa lấy ở trên):
```text
IP_CPANEL wp.diemly.vietnix.tech
IP_CPANEL laravel.diemly.vietnix.tech
```
* **Lưu và thoát:** Nhấn `Ctrl + O` ➡️ Nhấn `Enter` ➡️ Nhấn `Ctrl + X`.

---

### Bước 4.2: Di chuyển chứng chỉ bảo mật SSL (SSL Migration)
Chúng ta sẽ vào VPS cũ, copy SSL và đem dán vào cPanel.

**1. Cài đặt SSL cho WordPress:**
* Mở lại Tab Terminal đang SSH vào VPS cũ (`root@221.132.21.142`).
* Đọc nội dung chứng chỉ (CRT) bằng lệnh:
```bash
cat /etc/letsencrypt/live/wp.diemly.vietnix.tech/cert.pem
```
* (Dùng chuột bôi đen và Copy toàn bộ khối chữ từ `-----BEGIN CERTIFICATE-----` cho đến hết `-----END CERTIFICATE-----`).
* Mở cPanel trên trình duyệt ➡️ Về trang chủ tìm mục **SSL/TLS Certificate** ➡️ **Installation**.
* Cuộn xuống phần *Install an SSL Website*, ô **Domain** chọn `wp.diemly.vietnix.tech`.
* Dán khối chữ vừa copy vào ô **Certificate: (CRT)**.
* Quay lại Terminal VPS, đọc nội dung khóa riêng (KEY) bằng lệnh:
```bash
cat /etc/letsencrypt/live/wp.diemly.vietnix.tech/privkey.pem
```
* (Copy toàn bộ khối chữ `-----BEGIN PRIVATE KEY-----` đến hết).
* Dán khối chữ này vào ô **Private Key (KEY)** trên cPanel.
* Cuộn xuống dưới cùng bấm nút **Install Certificate**. Báo success màu xanh là xong 1 web!

**2. Cài đặt SSL cho Laravel:**
* Vẫn ở trang Manage SSL sites trên cPanel, cuộn xuống chọn **Domain** là `laravel.diemly.vietnix.tech`.
* Về Terminal VPS, lấy chứng chỉ CRT của Laravel:
```bash
cat /etc/letsencrypt/live/laravel.diemly.vietnix.tech/cert.pem
```
* (Copy và dán vào ô CRT).
* Lấy Private KEY của Laravel:
```bash
cat /etc/letsencrypt/live/laravel.diemly.vietnix.tech/privkey.pem
```
* (Copy và dán vào ô KEY).
* Bấm **Install Certificate**.

---

### 🎉 BƯỚC NGHIỆM THU CUỐI CÙNG
Gõ lần lượt 2 tên miền, và hiển thị đúng 2 website:
* `https://wp.diemly.vietnix.tech`
* `https://laravel.diemly.vietnix.tech`
