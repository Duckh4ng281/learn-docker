# Swarm Storage

## 1. Thách thức Storage trong Swarm

Trong môi trường đơn host, volume là giải pháp lưu trữ bền vững đơn giản. Tuy nhiên khi chạy trong Swarm cluster với nhiều node, storage trở nên phức tạp hơn do container có thể được lập lịch trên bất kỳ node nào.

Vấn đề: nếu service có 3 replica trải đều trên 3 node, mỗi replica chỉ có thể truy cập volume trên node của nó, không có cơ chế chia sẻ volume giữa các node.

---

## 2. Local volume

Volume thông thường trong Swarm chỉ tồn tại trên node cụ thể nơi task đang chạy.

Hệ quả:
- Nếu task được reschedule sang node khác, nó sẽ tạo volume mới và mất dữ liệu cũ
- Không thể dùng local volume cho stateful service như database khi có nhiều replica
- Phù hợp cho dữ liệu không cần chia sẻ và không quan trọng nếu mất khi reschedule

---

## 3. Placement Constraint

Giải pháp đơn cho vấn đề trên trong Swarm là kết hợp local volume với placement constraint.

Bằng cách gắn label cho node lưu trữ dữ liệu và cấu hình service chỉ chạy trên node đó, dữ liệu luôn gắn với đúng node. Task khi reschedule sẽ chỉ được lập lịch lại trên cùng node đó.

Cách này làm mất khả năng tự động failover sang node khác khi node bị lỗi.

---

## 4. Shared Storage với Volume Driver

Để nhiều replica trên nhiều node cùng truy cập một nguồn lưu trữ, cần dùng shared storage thông qua volume driver.

Volume driver kết nối Docker với hệ thống lưu trữ bên ngoài, cho phép cùng một volume được mount vào container trên nhiều node khác nhau.

Các loại shared storage phổ biến:

**NFS (Network File System)**

Một NFS server cung cấp thư mục dùng chung, các node trong Swarm mount thư mục đó vào container thông qua NFS volume driver.

Phù hợp cho: file upload, shared config, log tập trung.

**Distributed File System (GlusterFS, CephFS)**

Hệ thống file phân tán cung cấp storage với khả năng chịu lỗi và hiệu năng cao hơn NFS. Dữ liệu được replicate trên nhiều storage node.

Phù hợp cho môi trường cần storage có high availability mà không phụ thuộc vào một NFS server duy nhất.

**Cloud Block Storage (AWS EBS, GCP Persistent Disk, Azure Disk)**

Trên môi trường cloud, block storage của nhà cung cấp có thể được attach vào node và dùng như volume.

---

## 5. Swarm Secrets

Docker Swarm cung cấp cơ chế Secrets để quản lý dữ liệu nhạy cảm một cách an toàn trong cluster.

Secrets khác với biến môi trường:
- Được mã hóa khi lưu trữ trong Raft store (encrypted at rest)
- Được mã hóa khi truyền giữa manager và worker (encrypted in transit)
- Chỉ được giải mã và mount vào container dưới dạng file trong RAM (tmpfs), không ghi xuống disk
- Chỉ node đang chạy task cần secret mới nhận được secret đó
- Không bao giờ expose qua biến môi trường hay log

Secret được mount vào container tại đường dẫn `/run/secrets/<tên-secret>`. Ứng dụng đọc file để lấy giá trị thay vì đọc biến môi trường.

---

## 6. Swarm Configs

Tương tự Secrets, Swarm Configs là cơ chế lưu trữ và phân phối file cấu hình không nhạy cảm đến container trong cluster.

Configs được lưu trong Raft store và phân phối đến node cần dùng.Configs không được mã hóa và mount vào filesystem thông thường của container.

Configs thường dùng cho: file cấu hình ứng dụng, certificate công khai, script khởi động...