# Swarm Service

## 1. Tổng quan

Service là đơn vị triển khai cơ bản trong Docker Swarm, mô tả cách một ứng dụng hoặc thành phần của ứng dụng được chạy trong cluster.

Khi tạo service, người dùng khai báo desired state: image nào, bao nhiêu replica, cấu hình network, volume, biến môi trường... Swarm manager nhận khai báo đó và chịu trách nhiệm duy trì trạng thái mong muốn trên toàn cluster.

---

## 2. Service và Container

Service không phải là container, service là định nghĩa ở mức cao hơn. Từ một service, Swarm tạo ra các task, mỗi task tương ứng với một container đang chạy trên một node cụ thể.

Mối quan hệ:

```
Service
  └── Task 1 → Container trên Node A
  └── Task 2 → Container trên Node B
  └── Task 3 → Container trên Node C
```

Người dùng quản lý ở cấp service, Swarm xử lý việc tạo, phân phối và duy trì task bên dưới.

---

## 3. Hai chế độ Service

**Replicated Service**

Replicated service chạy đúng số lượng replica được chỉ định, phân phối trên các node trong cluster.

Đây là chế độ mặc định và Swarm phân phối replica đều trên các node có thể nhận task.

**Global Service**

Global service chạy đúng một container trên mỗi node trong cluster.

Phù hợp với các agent cần chạy trên mọi node như: monitoring agent, log collector, security scanner... Khi node mới được thêm vào cluster, Swarm tự động tạo task của global service trên node đó.

---

## 4. Task

Task là đơn vị nhỏ nhất mà Swarm lập lịch. Mỗi task đại diện cho một container cần chạy trên một node cụ thể.

Vòng đời của task:

| Trạng thái | Mô tả |
|-----------|-------|
| **Pending** | Task đang chờ được lập lịch |
| **Assigned** | Task đã được giao cho một node |
| **Preparing** | Node đang chuẩn bị |
| **Running** | Container đang chạy |
| **Complete** | Container kết thúc thành công |
| **Failed** | Container kết thúc với lỗi |
| **Rejected** | Node từ chối task (không đủ tài nguyên, không khớp constraint) |
| **Shutdown** | Task bị dừng theo yêu cầu |

Khi task chuyển sang trạng thái Failed, Swarm tạo task mới thay thế để duy trì số replica mong muốn.

---

## 5. Scheduling

Khi cần tạo task mới, Swarm manager thực hiện quá trình lập lịch để quyết định task chạy trên node nào:

**Filtering**

Loại bỏ các node không đáp ứng yêu cầu:
- Node có availability Active
- Node có đủ tài nguyên (CPU, memory)
- Node khớp với placement constraint
- Node có thể pull image cần thiết

**Spreading**

Trong số node còn lại sau filtering, Swarm mặc định chọn node có ít task của service này nhất, phân phối đều tải trên cluster.

---

## 6. Load balancing

Swarm cung cấp load balancing tích hợp cho service thông qua Virtual IP (VIP).

Mỗi service được cấp một VIP cố định trong overlay network. Khi container gửi request đến tên service, DNS nội bộ resolve tên thành VIP. Swarm load balancer sau đó phân phối request đến các task đang healthy của service.

VIP không thay đổi dù replica bị restart hay reschedule.

---

## 7. Ingress Load Balancing

Swarm cung cấp ingress load balancing để xử lý traffic từ bên ngoài vào service.

Khi service publish một port, Swarm tạo ingress network và cấu hình mọi node trong cluster đều lắng nghe trên port đó, dù node đó có đang chạy replica của service hay không. Request đến bất kỳ node nào cũng được forward đến đúng task của service.
