# Swarm Networking

## 1. Tổng quan

Swarm Networking là hệ thống mạng trong Docker Swarm, cho phép container trên nhiều host khác nhau giao tiếp với nhau như thể đang ở cùng một mạng nội bộ, đồng thời cung cấp cơ chế load balancing và routing traffic từ bên ngoài vào service.

---

## 2. Overlay Network trong Swarm

Overlay network là loại network được dùng trong Swarm để kết nối container trên nhiều host.

Khi Swarm được khởi tạo, Docker tự động tạo một overlay network đặc biệt tên `ingress` dùng cho load balancing. Người dùng có thể tạo thêm overlay network riêng cho từng ứng dụng hoặc nhóm service.

Container trong cùng overlay network có thể giao tiếp với nhau qua IP nội bộ hoặc tên service, bất kể chúng đang chạy trên node nào trong cluster. VXLAN xử lý việc truyền packet qua mạng vật lý giữa các host trong suốt với ứng dụng.

---

## 3. Ingress Network và Routing Mesh

Ingress network là overlay network đặc biệt, tự động được tạo khi Swarm khởi tạo, dùng để thực hiện Routing Mesh.

Routing Mesh là cơ chế cho phép mọi node trong cluster nhận traffic cho bất kỳ service nào, dù node đó có đang chạy replica của service hay không.

Cơ chế hoạt động:
1. Service publish một port, Swarm mở port dó trên mọi node trong cluster
2. Client gửi request đến port đó của bất kỳ node nào
3. Nếu node đó đang chạy replica của service, request được xử lý tại chỗ
4. Nếu node đó không chạy replica, request được forward qua ingress network đến một node đang có replica
5. Swarm load balancer phân phối request đến các replica healthy theo thuật toán round-robin

---

## 4. User-defined Overlay Network

Ngoài ingress network, người dùng cần tạo overlay network riêng để kết nối service trong ứng dụng.

Overlay network do người dùng tạo hỗ trợ:
- Kết nối container trên nhiều node trong cùng mạng ảo
- DNS resolution theo tên service
- Cô lập giữa các ứng dụng
- Load balancing nội bộ qua VIP

Thường tạo overlay network riêng cho từng ứng dụng hoặc nhóm service liên quan, thay vì để tất cả dùng chung ingress network.

---

## 5. Service VIP và DNS

Trong Swarm overlay network, mỗi service được cấp:

- Virtual IP (VIP): một IP cố định đại diện cho service, không thay đổi dù replica được tạo lại
- DNS record: tên service được DNS nội bộ resolve thành VIP

Khi container A gọi đến tên service B:
1. DNS nội bộ (`127.0.0.11`) resolve `service-b` thành VIP của service B
2. Swarm IPVS (IP Virtual Server) load balancer nhận packet đến VIP
3. IPVS chọn một task healthy của service B và forward packet đến IP thực của task đó
4. Packet đến container đang chạy task, xử lý và phản hồi

---

## 6. Network Isolation trong Swarm

Cô lập network trong Swarm hoạt động theo hai cấp:

**Service level**

Service chỉ giao tiếp được với service khác trong cùng overlay network. Hai service ở các overlay network khác nhau không thể kết nối trực tiếp.

**Stack level**

Khi deploy bằng Docker Stack, mỗi stack có overlay network riêng. Service giữa các stack không giao tiếp được trừ khi được cấu hình rõ ràng để dùng chung network.