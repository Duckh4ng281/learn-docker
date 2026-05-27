# Service Discovery

## 1. Tổng quan

Service Discovery là cơ chế giúp các service hoặc container trong hệ thống có thể tự động tìm thấy và kết nối với nhau mà không cần biết trước địa chỉ IP cố định.

Trong môi trường container, địa chỉ IP không ổn định và không phải là định danh tin cậy để kết nối service. Service discovery giải quyết vấn đề này bằng cách dùng tên thay vì IP.

---

## 2. Vấn đề khi không có Service Discovery

Trong môi trường container, IP của container thay đổi trong nhiều tình huống:

- Container bị restart
- Container bị recreate sau khi deploy phiên bản mới
- Hệ thống scale up/down
- Container di chuyển sang host khác trong cluster

Nếu service A hard-code IP của service B, kết nối sẽ bị gián đoạn mỗi khi service B thay đổi IP. Đây là mô hình không phù hợp với đặc tính động của container.

---

## 3. Cơ chế Service Discovery trong Docker

**Embedded DNS Server**

Docker cung cấp một DNS server nội bộ cho các container trong user-defined network. DNS server này lắng nghe tại địa chỉ `127.0.0.11` trong mỗi container.

Khi container được tạo và gắn vào một user-defined network, Docker tự động:
- Đăng ký tên container vào DNS server nội bộ
- Cấu hình `/etc/resolv.conf` của container để trỏ đến DNS server nội bộ

Từ đó, container có thể resolve tên của container khác bằng cách gửi DNS query đến `127.0.0.11`.

Default bridge network (`docker0`) không hỗ trợ DNS resolution theo tên. Để dùng service discovery, cần tạo user-defined bridge network.

---

## 4. Service Discovery trong Docker Compose

Docker Compose tự động tạo user-defined network cho mỗi project. Nhờ đó, các service trong Compose file có thể giao tiếp với nhau bằng tên service được khai báo trong file cấu hình.

---

## 5. Service Discovery trong Docker Swarm

Trong Docker Swarm, service discovery hoạt động ở mức cao hơn với Virtual IP (VIP).

Mỗi Swarm service được cấp một VIP cố định. DNS nội bộ của overlay network resolve tên service thành VIP này. Load balancer của Swarm phân phối request từ VIP đến các container đang chạy service.
