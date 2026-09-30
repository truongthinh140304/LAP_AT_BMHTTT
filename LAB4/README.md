# LAB4 — Khảo sát mạng Host-only bằng Nmap

## 1\. Thông tin sinh viên

* **Họ và tên:** Nguyễn Trường Thịnh
* **MSSV:** 1150080157
* **Tên lab:** LAB4 — Khảo sát mạng nội bộ bằng Nmap



## 2\. Phiên bản và phạm vi môi trường

|Thành phần|Thông tin đã xác nhận|Vai trò|
|-|-|-|
|Máy thật|Windows, VMware Workstation|Chạy máy ảo và mạng Host-only|
|Kali Linux|Kali 2026.2, Nmap 7.99, `eth0` `192.168.204.128/24`|Máy quét|
|Metasploitable 2|`eth1` `192.168.204.131/24`|Máy đích trong lab|
|Windows 10 VM|Có trong VMware, chưa có IP/kết quả quét|Không dùng trong kết quả bên dưới|
|Mạng lab|VMware Host-only `192.168.204.0/24`|Cô lập phạm vi quét|

Windows Server và Sophos không thuộc phạm vi thực hiện. Phiên bản chính xác của VMware và cấu hình RAM/CPU chưa được ghi nhận.

## 3\. Cách dựng môi trường

1. Mở Kali Linux và Metasploitable 2 trong VMware Workstation.
2. Chọn mạng **Host-only** cho Kali. Trên Metasploitable 2, ngắt adapter NAT và giữ adapter Host-only.
3. Trên Metasploitable 2, bật `eth1` và xin IP bằng `sudo ifconfig eth1 up`, `sudo dhclient eth1`; kiểm tra bằng `ifconfig eth1`.
4. Kiểm tra IP Kali với `ip -br addr`; hai máy cùng dải `192.168.204.0/24`.
5. Ping từ Kali tới `192.168.204.131` để xác nhận kết nối trước khi quét.

## 4\. Tình huống đã thực hiện và kết quả PASS/FAIL

|Tình huống|Kết quả quan sát|Đánh giá|
|-|-|-|
|Kiểm tra công cụ Nmap|Nmap 7.99 trên Kali|PASS|
|Xác định IP Host-only|Kali `.128`; Metasploitable 2 `.131`|PASS|
|Kiểm tra kết nối|`ping -c 4 192.168.204.131`: nhận 4/4, mất 0%|PASS|
|Tìm host trong dải|`sudo nmap -sn -n -e eth0 192.168.204.0/24`: `.1`, `.131`, `.254` đang hoạt động|PASS|
|Quét TCP SYN|`sudo nmap -sS -Pn -n 192.168.204.131`: 23 cổng TCP mở|PASS|
|Nhận diện dịch vụ|`-sV`: thấy Apache httpd 2.2.8, MySQL 5.0.51a-3ubuntu5, PostgreSQL 8.3.0–8.3.7, v.v.|PASS|
|Nhận diện hệ điều hành|`-O`: Nmap dự đoán Linux 2.6.9–2.6.33|PASS |
|NSE thu thập thông tin SMB|`smb-os-discovery` trên 445/tcp: Unix, Samba 3.0.20-Debian, tên `metasploitable`, domain `localdomain`|PASS|
|Xuất tệp kết quả|`/home/kali/nmap\_metasploitable.txt` tồn tại, ảnh `ls -lh` hiển thị 914 byte|PASS|

Các cổng TCP mở được thấy trong ảnh quét: `21`, `22`, `23`, `25`, `53`, `80`, `111`, `139`, `445`, `512`, `513`, `514`, `1099`, `1524`, `2049`, `2121`, `3306`, `5432`, `5900`, `6000`, `6667`, `8009`, `8180`.

## 5\. Lỗi gặp phải và cách khắc phục

|Hiện tượng|Nguyên nhân / xử lý|Kết quả|
|-|-|-|
|Ban đầu Kali không ping được `192.168.204.131`|Metasploitable 2 chưa hoạt động hoặc `eth1` chưa có IP; bật máy, bật `eth1`, xin DHCP lại|Ping sau đó thành công 4/4|
|Metasploitable 2 có IP NAT `192.168.145.131` ở `eth0`|IP NAT khác dải Host-only của Kali; ngắt NAT và dùng `eth1` Host-only `.131`|Hai VM cùng dải `.204.0/24`|
|Lệnh `nmap -sn` ban đầu chạy lâu, chưa trả kết quả|Dừng bằng Ctrl+C; quét lại với quyền sudo, tắt phân giải DNS và chỉ rõ `eth0`|Tìm được 3 host trong khoảng 1,91 giây|
|Ảnh Terminal dài nên mất phần đầu lệnh|Chụp thêm phần đầu output hoặc cuộn Terminal để thấy lệnh và IP|Cần kiểm tra đủ minh chứng trước khi nộp|



