# Overlay Network

## 1. Tổng quan

Overlay Network là loại network trong Docker cho phép các container giao tiếp với nhau trên nhiều host khác nhau thông qua một mạng ảo được xây dựng trên lớp mạng vật lý.

Overlay Network thường được sử dụng trong các hệ thống phân tán như Docker Swarm, nơi container có thể được phân phối trên nhiều node vật lý.

---

## 2. Vấn đề Overlay Network giải quyết

Trong môi trường đơn host, bridge network đủ để kết nối các container. Tuy nhiên khi hệ thống mở rộng ra nhiều host, bridge network không thể kết nối container trên các host khác nhau vì mỗi bridge chỉ hoạt động trong phạm vi một host.

Overlay Network giải quyết vấn đề này bằng cách tạo ra một lớp mạng ảo trên nhiều host, cho phép container trên các host khác nhau giao tiếp như thể đang ở cùng một mạng nội bộ.

---

## 3. Cơ chế hoạt động

Overlay Network trong Docker sử dụng VXLAN (Virtual Extensible LAN) để đóng gói và truyền traffic giữa các host.

Khi container A trên host 1 gửi packet đến container B trên host 2:

1. Packet từ container A đi qua veth vào overlay network interface trên host 1
2. VXLAN driver đóng gói packet gốc bên trong một UDP packet mới, với header chứa địa chỉ IP của host 2
3. UDP packet được truyền qua mạng vật lý từ host 1 đến host 2
4. Trên host 2, VXLAN driver giải đóng gói UDP packet, lấy ra packet gốc
5. Packet gốc được forward vào network namespace của container B

Từ góc nhìn của container, chúng đang giao tiếp trực tiếp trên cùng một mạng, hoàn toàn không biết đến quá trình đóng/giải gói bên dưới.

---

## 4. Control Plane và Data Plane

Overlay Network bao gồm hai thành phần:

**Control Plane**

Control plane chịu trách nhiệm quản lý topology mạng: biết container nào đang chạy trên host nào, IP nội bộ là gì.

Trong Docker Swarm, control plane sử dụng distributed key-value store để đồng bộ thông tin mạng giữa các Swarm node. Khi container mới được tạo hoặc di chuyển, thông tin mạng được cập nhật trên toàn cluster.

**Data Plane**

Data plane chịu trách nhiệm thực sự truyền dữ liệu giữa container. Đây là nơi quá trình đóng gói và giải đóng gói của VXLAN diễn ra.

---

## 5. Cấu trúc mạng Overlay

Trong một Overlay Network:

- Mỗi node tham gia overlay có một VTEP (VXLAN Tunnel Endpoint), interface chịu trách nhiệm đóng/giải gói VXLAN
- Mỗi container được cấp một IP nội bộ trong subnet của overlay network, độc lập với IP của host
- Các container trong cùng overlay network có thể giao tiếp qua IP nội bộ hoặc tên service

---

## 6. Service Discovery trong Overlay Network

Overlay Network hỗ trợ service discovery dựa trên tên service trong Docker Swarm.

Thay vì dùng IP cố định, container giao tiếp với nhau thông qua tên service. Docker Swarm cung cấp virtual IP (VIP) cho mỗi service, DNS nội bộ của overlay network resolve tên service thành VIP, sau đó phân phối request đến các replica của service đó.

Cơ chế này giúp service tiếp tục hoạt động dù container cụ thể bị khởi động lại hay di chuyển sang host khác, client chỉ cần biết tên service.
