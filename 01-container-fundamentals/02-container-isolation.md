# Container Isolation

## 1. Tổng quan

Container isolation là cơ chế cô lập môi trường thực thi của từng container, giúp mỗi container hoạt động độc lập với các container khác và với hệ thống host.

Mặc dù các container chia sẻ chung kernel của hệ điều hành host, nhưng chúng vẫn được tách biệt về tiến trình, mạng, hệ thống file và quyền truy cập tài nguyên nhờ các cơ chế của Linux kernel.

---

## 2. Mục tiêu của cơ chế cô lập

Cơ chế isolation trong container được thiết kế nhằm:

- Đảm bảo các container không can thiệp lẫn nhau khi chạy chung một hệ thống
- Tạo môi trường thực thi độc lập cho từng ứng dụng
- Tăng tính ổn định của hệ thống khi chạy nhiều container
- Giới hạn phạm vi ảnh hưởng khi xảy ra lỗi hoặc tấn công

---

## 3. Các lớp cô lập trong container

Container isolation được xây dựng theo nhiều lớp khác nhau ở mức hệ điều hành.

**Process isolation (cô lập tiến trình)**

Mỗi container có không gian tiến trình riêng. Process bên trong container không nhìn thấy và không can thiệp được vào process của container khác hoặc của host. Process đầu tiên trong container có PID 1 trong không gian của nó, dù trên host nó có thể có PID hoàn toàn khác.

**Network isolation (cô lập mạng)**

Mỗi container có stack mạng riêng bao gồm interface, địa chỉ IP, routing table và firewall rules. Điều này giúp container hoạt động như một máy độc lập về mặt mạng, có thể dùng cùng port number mà không xung đột với container khác.

**Filesystem isolation (cô lập hệ thống file)**

Container có filesystem riêng được tạo từ các layer filesystem. Container không thể truy cập trực tiếp vào filesystem của host nếu không được mount rõ ràng. Điều này ngăn container ghi đè hay đọc dữ liệu nhạy cảm của hệ thống.

**User isolation (cô lập người dùng)**

Container có thể ánh xạ user riêng biệt với hệ thống host thông qua user namespace. Một process chạy với quyền root bên trong container có thể được ánh xạ thành một user không có quyền trên host, tăng mức độ an toàn.

---

## 4. Namespace 

Namespace là cơ chế của Linux kernel tạo ra sự cô lập trong container. Mỗi container được gán một tập namespace riêng để tách biệt tài nguyên hệ thống. Khi một process nằm trong một namespace, nó chỉ có thể nhìn thấy và tương tác với các tài nguyên thuộc namespace đó.

Linux hiện hỗ trợ 8 loại namespace:

| Namespace | Cô lập |
|-----------|--------|
| PID | Không gian tiến trình |
| NET | Stack mạng (interface, routing, iptables...) |
| MNT | Mount points và filesystem |
| UTS | Hostname và domain name |
| USER | User ID và Group ID |
| IPC | Giao tiếp giữa các tiến trình |
| TIME | Đồng hồ hệ thống |
| CGROUP | Sơ đồ phân chia và giới hạn tài nguyên |

---

## 5. Cgroups 

Cgroups (Control Groups) là cơ chế dùng để giới hạn và quản lý tài nguyên hệ thống cho từng container.

Cgroups không tạo sự cô lập hoàn toàn về môi trường, nhưng đảm bảo mỗi container chỉ sử dụng tài nguyên trong giới hạn được cấp phát. Điều này ngăn một container chiếm dụng CPU hay RAM của toàn hệ thống, ảnh hưởng đến các container khác.
