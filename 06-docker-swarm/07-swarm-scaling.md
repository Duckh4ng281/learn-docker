# Swarm Scaling

## 1. Tổng quan

Scaling trong Docker Swarm là khả năng tăng hoặc giảm số lượng replica của một service để đáp ứng thay đổi về tải, mà không gây gián đoạn dịch vụ.

Swarm xử lý việc tạo hoặc xóa task, phân phối chúng đến các node phù hợp và đảm bảo load balancing tự động điều chỉnh theo số replica mới.

---

## 2. Horizontal Scaling

Swarm sử dụng horizontal scaling, thêm hoặc bớt số lượng replica của service, thay vì tăng tài nguyên của từng container.

Khi scale up:
1. Swarm tính toán số task cần tạo thêm
2. Manager lập lịch task mới trên các node có tài nguyên phù hợp
3. Mỗi task pull image và khởi chạy container
4. Khi container healthy, load balancer tự động đưa replica mới vào nhận traffic

Khi scale down:
1. Swarm chọn task cần xóa
2. Task bị shutdown
3. Load balancer loại replica đã xóa khỏi nhận traffic

---

## 3. Phân phối Replica trên Cluster

Khi scale service, Swarm phân phối replica mới đến các node dựa trên chiến lược spreading mặc định: ưu tiên node có ít task của service đó nhất.

Kết quả là replica được phân phối tương đối đều trên các node đủ điều kiện, tránh tình trạng một node nhận quá nhiều task trong khi node khác nhàn rỗi.

Nếu cluster không có đủ node thỏa mãn placement constraint, Swarm sẽ để task ở trạng thái Pending cho đến khi có node phù hợp.

---

## 4. Global Service và Scaling

Global service không scale theo số replica, chúng tự động scale theo số node trong cluster.

Khi node mới được thêm vào cluster, Swarm tự động tạo task của global service trên node đó. Khi node bị xóa, task của global service trên node đó bị xóa theo.