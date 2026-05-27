# Docker Swarm Overview

## 1. Tổng quan

Docker Swarm là cơ chế orchestration tích hợp sẵn trong Docker, cho phép quản lý và vận hành một cluster nhiều Docker host như một hệ thống thống nhất.

Thay vì quản lý từng container trên từng host riêng lẻ, Swarm cho phép khai báo trạng thái mong muốn của hệ thống và tự động phân phối, duy trì trạng thái đó trên toàn cluster.

---

## 2. Vấn đề Swarm giải quyết

Khi ứng dụng phát triển, một Docker host đơn lẻ không còn đủ để đáp ứng về hiệu năng, tính sẵn sàng và khả năng chịu lỗi.

Các vấn đề:

- Một host bị lỗi kéo theo toàn bộ ứng dụng ngừng hoạt động
- Không thể tăng capacity bằng cách thêm host mà không có cơ chế điều phối
- Quản lý thủ công nhiều host dễ xảy ra sai sót và không nhất quán
- Không có cơ chế tự động khôi phục khi container hay host gặp sự cố

Docker Swarm giải quyết tất cả các vấn đề này bằng cách tạo ra một lớp orchestration tự động hóa việc phân phối và duy trì container trên nhiều host.

---

## 3. Kiến trúc tổng quan

Docker Swarm hoạt động theo mô hình manager-worker:

- **Manager node**: quản lý trạng thái cluster, lập lịch task, xử lý API request
- **Worker node**: thực thi task theo chỉ thị của manager

Một cluster Swarm có thể có nhiều manager để đảm bảo high availability, nhưng chỉ một manager giữ vai trò leader tại một thời điểm. Mọi worker đều nhận lệnh từ manager và báo cáo trạng thái ngược lại.

---

## 4. Desired State và Reconciliation

Điểm cốt lõi của Swarm là cơ chế desired state reconciliation.

Người dùng khai báo trạng thái mong muốn. Swarm liên tục so sánh trạng thái thực tế với trạng thái mong muốn và tự động thực hiện hành động để khắc phục sự khác biệt.

Ví dụ: nếu một container bị crash, Swarm phát hiện số replica giảm xuống còn 2 và tự động tạo container mới để đưa về đúng 3 replica mà không cần can thiệp thủ công.

---

## 5. Tính năng chính của Docker Swarm

- **Cluster management**: quản lý nhiều Docker host như một đơn vị duy nhất
- **Service scaling**: tăng/giảm số replica của service bằng một lệnh
- **Load balancing**: phân phối request đến các replica của service tự động
- **Rolling update**: cập nhật service mà không gây downtime
- **Self-healing**: tự động restart container khi bị crash, reschedule khi node bị lỗi
- **Secret management**: quản lý dữ liệu nhạy cảm an toàn trong cluster
- **Overlay networking**: kết nối container trên nhiều host trong cùng mạng ảo
