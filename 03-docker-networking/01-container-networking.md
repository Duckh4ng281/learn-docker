# Container Networking

## 1. Tổng quan

Container networking là cơ chế cung cấp khả năng giao tiếp mạng cho container thông qua Linux network stack, được cô lập bằng network namespace.

Container không có network stack riêng ở mức kernel. Thay vào đó, mỗi container được gán một network namespace riêng để tạo ra một mạng độc lập trong khi vẫn chia sẻ kernel network stack của host.

---

## 2. Network Namespace

Network namespace là nền tảng của container networking.

Mỗi network namespace cung cấp một môi trường mạng hoàn toàn tách biệt, bao gồm:

- Network interface riêng
- Routing table riêng
- Iptables rules riêng
- Socket table riêng
...

Từ góc nhìn của container, network namespace tạo ra một hệ thống mạng độc lập. Tuy nhiên tất cả vẫn chạy trên cùng một kernel network stack của host.

---

## 3. Virtual Ethernet (veth pair)

veth pair là cơ chế để kết nối container với host network.

veth hoạt động như một cặp network interface ảo kết nối với nhau:

- Một đầu nằm trong network namespace của container
- Một đầu nằm trên host namespace

Mọi packet đi vào một đầu sẽ xuất hiện ngay lập tức ở đầu còn lại. Đây là cơ chế virtual cable kết nối container với host network.

---

## 4. Port Mapping

Port mapping là cơ chế ánh xạ port từ host vào container, cho phép dịch vụ bên trong container được truy cập từ bên ngoài.

Ví dụ: `8080:80 (host:container)`

Khi có packet đến port 8080 của host, kernel ghi lại destination IP và port trước khi forward vào network namespace của container.

---


## 5. DNS trong Container Networking

Docker cung cấp embedded DNS server cho container trong user-defined network.

Cơ chế hoạt động:
- Docker Daemon chạy một DNS server nội bộ, mặc định lắng nghe tại `127.0.0.11`
- Mỗi container được cấu hình để dùng DNS server này
- DNS server resolve tên container hoặc tên service thành IP nội bộ hiện tại
- Khi container được tạo với IP mới, DNS record tự động cập nhật

Tính năng này chỉ có trên user-defined network.