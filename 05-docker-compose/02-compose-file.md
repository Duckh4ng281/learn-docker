# Compose File

## 1. Tổng quan

Compose File là file cấu hình trung tâm của Docker Compose, dùng để định nghĩa toàn bộ ứng dụng multi-container trong một file duy nhất.

File này có tên là `docker-compose.yml` và được viết theo định dạng YAML. Compose file thường được lưu trong thư mục gốc của project và version control cùng source code.

---

## 2. Vai trò của Compose File

Compose File giúp:

- Mô tả toàn bộ hệ thống multi-container trong một file duy nhất, dễ đọc và dễ hiểu
- Chuẩn hóa cấu hình môi trường
- Version control cùng source code
- Tái tạo môi trường nhanh chóng trên bất kỳ máy nào có Docker

---

## 3. Cấu trúc tổng quan

Một Compose File thường bao gồm ba phần chính:

- **services**: định nghĩa các container sẽ chạy
- **networks**: định nghĩa các network được dùng
- **volumes**: định nghĩa các volume được dùng

---

## 4. Services

Services là phần quan trọng nhất trong Compose File. Mỗi service đại diện cho một container (hoặc nhóm container có cùng cấu hình khi scale).

Mỗi service có thể khai báo:

- **image**: image Docker để chạy container
- **build**: đường dẫn Dockerfile để build image thay vì dùng image sẵn có
- **ports**: port mapping từ host vào container
- **environment**: biến môi trường truyền vào container
- **volumes**: volume hoặc bind mount gắn vào container
- **networks**: network mà service tham gia
- **depends_on**: service nào phải được khởi động trước service này
- **restart**: chính sách restart khi container gặp lỗi

---

## 5. Networks

Nếu không khai báo network, Compose tự động tạo một default network cho project và gắn tất cả service vào đó. Các service có thể giao tiếp với nhau qua tên service ngay lập tức.

Khi cần kiểm soát topology mạng phức tạp hơn, có thể khai báo network tùy chỉnh trong phần `networks`.

---

## 6. Volumes

Volumes được khai báo ở hai cấp:

- **Service level** (`volumes` trong service): chỉ định đường dẫn mount trong container và nguồn dữ liệu (volume name hoặc bind path)
- **Top-level** (`volumes` ở cấp cao nhất): định nghĩa named volume để Compose quản lý

Named volume được khai báo ở top-level có thể chia sẻ giữa nhiều service và tồn tại độc lập với lifecycle của container.

---

## 7. Dependencies giữa Services

`depends_on` cho phép khai báo service nào phải khởi động trước. `depends_on` chỉ đảm bảo thứ tự khởi động container, không đảm bảo service đã sẵn sàng để xử lý request.

---

## 8. Environment Configuration

Compose File hỗ trợ nhiều cách cấu hình biến môi trường:

- Khai báo trực tiếp trong file (chỉ phù hợp với giá trị không nhạy cảm)
- Đọc từ file `.env` trong cùng thư mục
- Tham chiếu đến biến môi trường của host

Cơ chế này giúp tách cấu hình khỏi code và dễ dàng thay đổi giá trị theo môi trường mà không sửa Compose file.