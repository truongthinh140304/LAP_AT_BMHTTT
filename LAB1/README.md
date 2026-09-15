# LAB1

- **Họ và tên sinh viên:** Nguyễn Trương Thịnh
- **Mã số sinh viên:** 1150080157
- **Tên bài Lab:** Examining SSH and Telnet in Wireshark

## Nội dung đã thực hiện

- Cài đặt và cấu hình dịch vụ Telnet, SSH trên Kali Linux.
- Tạo tài khoản `truongthinh` với mật khẩu là mã số sinh viên.
- Kiểm tra kết nối giữa các máy ảo bằng lệnh `ping`.
- Sử dụng PuttY để kết nối Telnet và SSH từ Windows đến Kali Linux.
- Dùng Wireshark bắt và phân tích các gói tin Telnet, SSH.
- Tạo khóa SSH bằng PuttY Key Generator.
- Cấu hình đăng nhập SSH bằng khóa công khai.

## Kết quả thực hiện

- Các máy ảo kết nối được với nhau trong cùng mạng.
- Kết nối Telnet qua cổng `23` thành công.
- Wireshark đọc được tài khoản và mật khẩu truyền qua Telnet dưới dạng văn bản rõ.
- Kết nối SSH qua cổng `22` thành công.
- Nội dung đăng nhập SSH được mã hóa nên Wireshark không đọc được mật khẩu.
- Đã tạo và cấu hình thành công khóa SSH cho tài khoản `truongthinh`.

## Lưu ý

- Địa chỉ IP Kali Linux khi thực hành: `192.168.204.128`.
- Tài khoản đăng nhập: `truongthinh`.
