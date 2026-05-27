# Compose Networking

## 1. Tổng quan

Compose Networking là cơ chế mạng được Docker Compose tự động tạo và quản lý để các service trong cùng một ứng dụng có thể giao tiếp với nhau.

Mỗi Compose project được gắn với một hoặc nhiều network riêng, giúp cô lập và tổ chức hệ thống mạng theo từng ứng dụng.

---

## 2. Default Network

Khi chạy Docker Compose, nếu không khai báo network, Docker tự động tạo một bridge network riêng cho project với tên theo format:

`<tên-thư-mục-project>_default`

Tất cả service được gắn vào network này. Các service có thể giao tiếp với nhau bằng tên service mà không cần cấu hình thêm.

---

## 3. Service-to-Service communication

Trong Compose networking, service giao tiếp với nhau qua tên service được khai báo trong Compose file.

Đây là lý do hardcode IP trong cấu hình ứng dụng không phù hợp với Docker Compose, tên service ổn định hơn và không thay đổi khi container được recreate.

---

## 4. DNS trong Docker Compose

Docker Compose cung cấp DNS nội bộ (`127.0.0.11`) cho các service trong cùng network.

DNS server này tự động:
- Cập nhật record khi container mới được tạo hoặc container cũ bị xóa
- Resolve tên service thành IP hiện tại của container đang chạy

---

## 5. Custom Networks trong Compose

Compose cho phép định nghĩa network tùy chỉnh để kiểm soát topology mạng.

Lợi ích của custom network:
- Tách biệt các nhóm service không cần giao tiếp với nhau
- Kiểm soát chính xác service nào có thể kết nối với service nào
- Hỗ trợ thiết kế multi-tier architecture rõ ràng hơn

---

## 6. Port Mapping trong Compose Networking

Mạng nội bộ của Compose được dùng cho giao tiếp giữa các service. Port mapping chỉ cần thiết khi muốn expose service ra ngoài để truy cập từ host hay internet.

---

## 7. Network Isolation giữa các Project

Mỗi Compose project có network riêng biệt với tên khác nhau.

Điều này đảm bảo:
- Service của project này không thể truy cập service của project khác theo mặc định
- Có thể chạy nhiều Compose project trên cùng host mà không lo xung đột mạng nội bộ

Nếu hai project cần giao tiếp với nhau, phải khai báo network chung và gắn service từ cả hai project vào network đó.
