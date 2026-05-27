# Host Network

## 1. Tổng quan

Host Network là chế độ mạng trong Docker cho phép container sử dụng trực tiếp network stack của host mà không cần tạo network namespace riêng.

Trong chế độ này, container không có IP riêng mà dùng chung IP, port và toàn bộ network interface với máy host.

---

## 2. Cơ chế hoạt động

Trong host network, Docker không tạo network namespace riêng cho container và không tạo veth pair hay bridge.

Thay vào đó:
- Process trong container chạy trực tiếp trong network namespace của host
- Container chia sẻ toàn bộ routing table, interface mạng, firewall rules của host
- Port mà ứng dụng lắng nghe trong container là port trực tiếp trên host, không cần port mapping

---

## 3. Khi nào nên dùng Host Network

Host network phù hợp với một số trường hợp:

- **Monitoring và network agent**: các công cụ cần đọc trạng thái mạng của host thường cần chạy trong host network để thấy được toàn bộ interface
- **High-performance networking**: khi ứng dụng xử lý lưu lượng mạng lớn với độ trễ thấp
- **Debugging network**: khi cần kiểm tra hành vi mạng của container trong điều kiện gần với môi trường thực nhất

