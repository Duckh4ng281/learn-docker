# Port Mapping

## 1. Tổng quan

Port Mapping là cơ chế trong Docker cho phép ánh xạ một port trên host ra một port trong container, giúp dịch vụ bên trong container có thể được truy cập từ bên ngoài.

Container chạy trong mạng cô lập với địa chỉ IP nội bộ không thể truy cập trực tiếp từ bên ngoài host. Port mapping là cầu nối giữa môi trường mạng bên trong container và mạng bên ngoài.

---

## 2. Cơ chế hoạt động

Port mapping được thực hiện bằng iptables DNAT (Destination NAT) rule trên host.

Khi một port mapping được thiết lập, ví dụ `8080:80`:

1. Docker thêm một iptables DNAT rule: packet đến port 8080 của host → rewrite destination thành IP nội bộ của container, port 80
2. Client bên ngoài gửi request đến `<host-IP>:8080`
3. Linux kernel intercept packet tại iptables, rewrite destination IP và port
4. Packet được forward vào network namespace của container đến port 80
5. Container xử lý request và phản hồi theo đường ngược lại


---

## 3. Các loại Port Binding

**Bind trên tất cả interface**

Mặc định, Docker bind port trên tất cả network interface của host (0.0.0.0). Request đến bất kỳ IP nào của host tại port đó đều được forward vào container.

**Bind trên interface cụ thể**

Có thể giới hạn chỉ bind trên một IP cụ thể của host, ví dụ chỉ bind trên localhost (127.0.0.1) để service chỉ truy cập được từ host, không expose ra mạng ngoài.

---

## 4. Port trong container

Mỗi container có không gian port riêng biệt nhờ network namespace. Điều này có nghĩa:

- Port trong container chỉ có ý nghĩa nội bộ trong network namespace của container đó
- Không thể truy cập trực tiếp từ bên ngoài nếu không có port mapping
- Docker không tự động expose port nếu không được cấu hình rõ ràng
- Nhiều container có thể dùng cùng port nội bộ mà không xung đột, vì mỗi container có network namespace riêng

---

## 5. Mapping nhiều container trên cùng host

Docker cho phép nhiều container chạy trên cùng host với các port mapping khác nhau, miễn là host port không trùng nhau:

Ví dụ: 

- Container A: `8080 → 80`
- Container B: `8081 → 80`


Mỗi container vẫn có thể dùng cùng port nội bộ (như port 80) vì chúng có network namespace riêng. Chỉ cần đảm bảo host port (8080, 8081) là duy nhất trên host.
