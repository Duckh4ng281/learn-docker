# Swarm Node

## 1. Tổng quan

Node là một Docker host tham gia vào Swarm cluster. Mỗi node chạy Docker Engine và được Swarm quản lý như một thành phần của cluster.

Node là đơn vị tài nguyên cơ bản trong Swarm, container được phân phối và chạy trên các node và Swarm lập lịch dựa trên tài nguyên sẵn có của từng node.

---

## 2. Manager node và Worker node

**Manager Node**

Manager node là node có quyền quản lý cluster. Manager chịu trách nhiệm:

- Duy trì trạng thái cluster thông qua distributed key-value store ư
- Nhận API request từ người dùng
- Lập lịch task, quyết định task nào chạy trên node nào
- Theo dõi trạng thái toàn cluster và thực hiện reconciliation

Manager node cũng có thể chạy task như worker, trừ khi được cấu hình drain để chỉ làm nhiệm vụ quản lý.

**Worker Node**

Worker node là node chỉ thực thi task theo chỉ thị của manager. Worker không tham gia vào việc quản lý cluster hay lập lịch.

Worker nhận task từ manager, chạy container tương ứng và báo cáo trạng thái ngược lại. Trong một cluster lớn, phần lớn node là worker.

---

## 3. Raft Consensus và Manager Quorum

Khi có nhiều manager, Swarm dùng thuật toán Raft để đảm bảo tính nhất quán của trạng thái cluster.

Raft yêu cầu quorum, đa số manager phải đồng thuận trước khi một thay đổi được ghi nhận. Công thức: với N manager, cần tối thiểu `(N/2) + 1` manager còn hoạt động để cluster tiếp tục vận hành.

---

## 4. Node Availability

Mỗi node có trạng thái availability xác định Swarm có lập lịch task mới cho node đó không:

| Availability | Ý nghĩa |
|-------------|---------|
| **Active** | Node sẵn sàng nhận task mới |
| **Pause** | Node không nhận task mới, task đang chạy vẫn tiếp tục |
| **Drain** | Node không nhận task mới, task đang chạy bị reschedule sang node khác |

`Drain` thường được dùng khi cần bảo trì node mà không gây downtime cho service.

---

## 5. Node Labels

Node label là metadata gắn vào node, dùng để kiểm soát việc lập lịch task.
