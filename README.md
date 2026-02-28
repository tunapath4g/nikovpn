# VPN MAX – Niko Chan VPN
# Mình đang bán gói này với giá 500k vĩnh viễn 
# 300k cho 6 tháng
# chỉ 50 slot gói 500k và 100 slot gói 300k
# Vào [web mua ngay](https://tunapath4g.github.io/nikovpn/) kẻo hết nhé!

## 1. VPN MAX hoạt động như thế nào

---

### Luồng hoạt động cơ bản

Client (điện thoại)  
&nbsp;&nbsp;&nbsp;&nbsp;│  
&nbsp;&nbsp;&nbsp;&nbsp;▼  
[V2Ray Core bên trong VPN MAX]  
&nbsp;&nbsp;&nbsp;&nbsp;│  Mã hóa AES-128-GCM / ChaCha20  
&nbsp;&nbsp;&nbsp;&nbsp;│  Header ngẫu nhiên chống fingerprint  
&nbsp;&nbsp;&nbsp;&nbsp;▼  
Kết nối WebSocket + TLS (giống HTTPS)  
&nbsp;&nbsp;&nbsp;&nbsp;▼  
[VPS Server]  
&nbsp;&nbsp;&nbsp;&nbsp;│  Giải mã  
&nbsp;&nbsp;&nbsp;&nbsp;│  Forward traffic thật  
&nbsp;&nbsp;&nbsp;&nbsp;▼  
Website đích (Google, YouTube, Facebook...)

---

### Quy trình chi tiết

1. Điện thoại chạy ứng dụng VPN MAX.  
2. Ứng dụng nhận traffic từ browser/app (YouTube, Facebook, v.v.).  
3. Traffic được:
   - Mã hóa bằng AES-128-GCM hoặc ChaCha20  
   - Thêm header ngẫu nhiên để chống fingerprinting  
4. Gói tin được gửi đến VPS thông qua:
   - WebSocket  
   - TLS (hiển thị như HTTPS thông thường)  
5. VPS:
   - Giải mã  
   - Forward đến website đích  
6. Response trả ngược theo chiều ngược lại.

> ⚠ Lưu ý: Giao thức yêu cầu đồng bộ thời gian hệ thống. Nếu lệch quá ~90 giây, kết nối có thể thất bại.

---

## 2. SNI Zero-Rating hoạt động như thế nào

### SNI là gì?

SNI (Server Name Indication) là extension của TLS cho phép client gửi hostname trước khi quá trình mã hóa hoàn tất, giúp server chọn đúng chứng chỉ SSL.

Trong TLS 1.2 (và TLS 1.3 khi chưa bật ECH), trường SNI **không được mã hóa** → ISP có thể đọc được.

---

## 3. Cơ chế khai thác Zero-Rating

Một số nhà mạng áp dụng chính sách zero-rating cho dịch vụ đối tác (ví dụ TV360).

Điều này có nghĩa:

> Traffic đến domain được whitelist sẽ không bị trừ dung lượng data.

---

### Cách cấu hình

1. Đăng ký gói nền miễn phí của dịch vụ được zero-rate.  
2. Cấu hình VPN MAX với:
   - SNI = domain được miễn phí - không cần nền ( đã được mã hóa nhà mạng )
3. Nhà mạng:
   - Đọc trường SNI trong TLS handshake  
   - Nếu domain nằm trong whitelist → không tính cước  
4. Thực tế:
   - Traffic bên trong tunnel được chuyển qua VPS  
   - Người dùng truy cập nội dung bất kỳ  

---

## 4. Sơ đồ tổng hợp cơ chế

[Điện thoại]  
&nbsp;&nbsp;&nbsp;&nbsp;│  App (YouTube, Facebook...)  
&nbsp;&nbsp;&nbsp;&nbsp;▼  
[VPN MAX Client]  
&nbsp;&nbsp;&nbsp;&nbsp;│  Mã hóa + SNI = videoakm1.tv360.vn  
&nbsp;&nbsp;&nbsp;&nbsp;▼  
[Trạm Nhà Mạng / DPI]  
&nbsp;&nbsp;&nbsp;&nbsp;│  Đọc SNI → thấy domain miễn phí  
&nbsp;&nbsp;&nbsp;&nbsp;│  Không trừ data ✓  
&nbsp;&nbsp;&nbsp;&nbsp;▼  
[VPS Server]  
&nbsp;&nbsp;&nbsp;&nbsp;│  Giải mã → Forward thật  
&nbsp;&nbsp;&nbsp;&nbsp;▼  
[Website đích]

---

## 5. Vì sao kỹ thuật này hoạt động

| Yếu tố | Giải thích |
|--------|------------|
| Zero-rating policy | Nhà mạng whitelist domain nhưng không kiểm tra nội dung tunnel |
| SNI plaintext | Trong TLS 1.2 và TLS 1.3 (chưa dùng ECH), SNI gửi dạng không mã hóa |
| Obfuscation | Traffic bên trong giống HTTPS thông thường |
| Giới hạn băng thông | Thường chỉ đủ dùng cơ bản, khó stream chất lượng cao |

---

## 7. Tổng kết

VPN MAX hoạt động bằng cách:

- Mã hóa traffic  
- Giả lập HTTPS hợp lệ  
- Lợi dụng cơ chế zero-rating dựa trên SNI  

Khi nhà mạng chỉ kiểm tra hostname thay vì nội dung thực, toàn bộ traffic được tunnel có thể không bị tính cước.
