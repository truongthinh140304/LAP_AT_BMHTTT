# LAB 6 — pfSense, Active Directory và chính sách truy cập

## 1\. Thông tin sinh viên

|Thông tin|Nội dung|
|-|-|
|Họ và tên|Nguyễn Trường Thịnh|
|MSSV|1150080157|
|Lớp|CNPM2|
|Tên lab|LAB 6 — pfSense, Active Directory và chính sách truy cập|
|Phạm vi|Kịch bản 1–5|

## 2\. Phiên bản môi trường

|Thành phần|Phiên bản / vai trò|
|-|-|
|VMware Workstation|Chưa ghi nhận số phiên bản|
|pfSense|Community Edition 2.7.2-RELEASE (amd64)|
|Windows Server|Windows Server 2022 Desktop Experience, DC01|
|Active Directory|Domain lab6.test, DNS trên DC01|
|Client-A / Client-B|Windows 10; chưa ghi nhận build|
|Web DMZ|Dự kiến Ubuntu Server và Nginx; chưa xác nhận phiên bản đã cài|

## 3\. Cách dựng môi trường

### Phân chia mạng

|Vùng mạng|Kết nối VMware|IP pfSense|
|-|-|-|
|WAN|VMnet8 — NAT 192.168.250.0/24|DHCP|
|MGMT|VMnet3 — Host-only 192.168.56.0/24|192.168.56.254/24|
|LAN|LAN segment lab6-lan|10.6.0.1/24|
|DMZ|LAN segment lab6-dmz|172.16.6.1/24|

Laptop quản trị dùng địa chỉ 192.168.56.1/24 trên MGMT. WebGUI pfSense: https://192.168.56.254.

### Địa chỉ máy

|Máy|IP|Gateway|DNS|Trạng thái|
|-|-|-|-|-|
|DC01|10.6.0.10/24|10.6.0.1|10.6.0.10|Đã xác nhận|
|Client-A|10.6.0.21/24|10.6.0.1|10.6.0.10| xác nhận|
|Client-B|10.6.0.22/24|10.6.0.1|10.6.0.10| xác nhận|
|DMZ-WEB|172.16.6.10, .20, .30, .40/24|172.16.6.1|Theo cấu hình DNS lab| xác nhận |

### Các bước triển khai

1. Tạo pfSense-LAB6 riêng và gắn bốn card mạng WAN, MGMT, LAN, DMZ theo bảng.
2. Đặt IP các interface; giữ đường quản trị HTTPS từ laptop qua MGMT.
3. Cài Windows Server 2022, đổi tên máy thành DC01 và đặt IP tĩnh 10.6.0.10.
4. Cài AD DS và DNS; tạo forest mới lab6.test.
5. Tạo OU LAB6-Users với sv\_a, sv\_b. Tạo tài khoản dịch vụ svc\_pfsence\_ldap trong Users.
6. Thêm LDAP server LAB6-AD trên pfSense với các thông số bên dưới; kiểm tra từng tài khoản bằng Diagnostics → Authentication.
7. Tạo Captive Portal zone LAB6\_USERS trên LAN, chọn Authentication backend LAB6-AD, Secondary để trống; lưu cấu hình và kiểm thử bằng client.
8. Cấu hình Client-A/B và web DMZ theo bảng địa chỉ.
9. Tạo alias, schedule, triển khai kịch bản 1–5; ghi kết quả thực tế và lưu ảnh minh chứng.

Các bước 7–9 cần bổ sung kết quả kiểm thử; danh sách trên mô tả quy trình, không đồng nghĩa toàn bộ đã hoàn tất.

### Cấu hình LDAP

|Mục|Giá trị|
|-|-|
|Authentication server|LAB6-AD|
|Host / Port|10.6.0.10 / 389|
|Transport / Protocol|Standard TCP / LDAP v3|
|Search Scope|Entire Subtree|
|Base DN|DC=lab6,DC=test|
|Authentication container|OU=LAB6-Users,DC=lab6,DC=test|
|Bind DN thực tế|CN=svc\_pfsence\_ldap,CN=Users,DC=lab6,DC=test|
|User naming attribute|samAccountName|

### Alias và Schedule dự kiến

|Alias|Giá trị|
|-|-|
|CLIENT\_A|10.6.0.21|
|CLIENT\_B|10.6.0.22|
|POLICY\_CLIENTS|10.6.0.21, 10.6.0.22|
|GENERAL\_SITE|172.16.6.10|
|JOB\_SITE|172.16.6.20|
|SOCIAL\_SITE|172.16.6.30|
|RESTRICTED\_SITE|172.16.6.40|
|WEB\_PORTS|80, 443|

|Schedule|Thời gian|
|-|-|
|LUNCH\_WINDOW|Thứ Hai–thứ Sáu, 11:30–13:00|
|WORK\_HOURS|Thứ Hai–thứ Sáu, 08:00–17:00|
|TEST\_NOW|Khoảng 10–15 phút chứa thời điểm thử|


## 4\. Nội dung đã thực hiện và kết quả PASS/FAIL

PASS chỉ ghi khi đã có kết quả quan sát. “Chưa kiểm thử” không được tính là PASS hoặc FAIL.

|Hạng mục|Kết quả thực tế|Đánh giá|
|-|-|-|
|IP DC01 và kết nối tới pfSense|DC01 10.6.0.10 ping 10.6.0.1 thành công, mất gói 0%|PASS|
|Tạo domain|lab6.test xuất hiện trong Active Directory Users and Computers|PASS|
|OU và người dùng|LAB6-Users có sv\_a và sv\_b|PASS|
|LDAP sv\_a|pfSense báo User sv\_a authenticated successfully|PASS|
|LDAP sv\_b|pfSense báo User sv\_b authenticated successfully|PASS|

