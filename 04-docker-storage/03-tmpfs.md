# tmpfs Mount

## 1. Tổng quan

tmpfs Mount là cơ chế lưu trữ trong Docker cho phép container ghi dữ liệu trực tiếp vào bộ nhớ RAM thay vì ghi xuống disk.

Dữ liệu trong tmpfs chỉ tồn tại trong bộ nhớ và sẽ bị mất hoàn toàn khi container dừng hoặc hệ thống khởi động lại. tmpfs không bao giờ được ghi xuống disk.

---

## 2. Vai trò của tmpfs Mount

tmpfs được sử dụng khi:

- Cần lưu dữ liệu tạm thời trong quá trình xử lý mà không muốn ghi xuống disk
- Muốn tăng hiệu năng I/O cho dữ liệu tạm (đọc/ghi từ RAM nhanh hơn disk nhiều lần)
- Cần tránh ghi dữ liệu nhạy cảm xuống disk
- Muốn giảm I/O trên disk trong các workload tạo và xóa file liên tục

---

## 3. Cơ chế hoạt động

Khi sử dụng tmpfs mount:

1. Kernel cấp phát một vùng bộ nhớ RAM cho tmpfs
2. Docker mount vùng nhớ này vào container tại đường dẫn được chỉ định
3. Container đọc/ghi dữ liệu vào đường dẫn đó như thư mục thông thường
4. Dữ liệu chỉ tồn tại trong RAM, không bao giờ được ghi xuống disk
5. Khi container dừng, vùng nhớ được giải phóng và toàn bộ dữ liệu biến mất

---

## 4. Đặc điểm của tmpfs Mount

- Dữ liệu lưu trong RAM: tốc độ đọc/ghi nhanh, không phụ thuộc vào tốc độ disk
- Không có persistence: dữ liệu biến mất khi container stop, dù container không bị xóa
- Giới hạn bởi RAM: dung lượng bị giới hạn bởi RAM hệ thống, có thể cấu hình kích thước tối đa
- Chỉ hoạt động trên Linux: tmpfs là tính năng của Linux kernel, không khả dụng trên Docker Desktop cho macOS hay Windows
- Không chia sẻ giữa container: không thể dùng cùng tmpfs mount trên nhiều container như volume

---

## 5. Giới hạn kích thước

Theo mặc định, Docker không giới hạn kích thước của tmpfs mount, container có thể dùng RAM tùy ý đến khi hết bộ nhớ hệ thống.

Để tránh tình trạng một container chiếm quá nhiều RAM, nên cấu hình kích thước tối đa khi tạo tmpfs. Giới hạn này đặt mức trần dung lượng mà tmpfs có thể sử dụng, bảo vệ các container và process khác trên cùng host.

