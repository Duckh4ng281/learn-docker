# Volume Management

## 1. Tổng quan

Volume Management trong Docker Compose là cơ chế cho phép khai báo và quản lý storage bền vững cho các service trong một ứng dụng multi-container, thông qua file cấu hình Compose.

Compose hỗ trợ tích hợp volume trực tiếp vào file cấu hình, giúp đơn giản hóa việc định nghĩa, chia sẻ và tái sử dụng storage.

---

## 2. Vai trò của Volume trong Compose

Volume trong Docker Compose được sử dụng để:

- Lưu trữ dữ liệu bền vững cho service
- Tách dữ liệu khỏi vòng đời container để không mất khi deploy phiên bản mới
- Chia sẻ dữ liệu giữa nhiều service trong cùng ứng dụng

---

## 3. Các loại Volume trong Compose

**Named Volume**

Named volume được Docker quản lý và đặt tên rõ ràng, khai báo ở top-level trong Compose file.

Đặc điểm:
- Docker lưu tại `/var/lib/docker/volumes/<project>_<tên>/`
- Tồn tại độc lập với container, không mất khi `docker compose down`
- Có thể chia sẻ giữa nhiều service

**Bind Mount**

Bind mount ánh xạ trực tiếp một đường dẫn trên host vào container, khai báo trong phần `volumes` của service với đường dẫn tuyệt đối hoặc tương đối.

Đặc điểm:
- Dữ liệu nằm tại đường dẫn cụ thể trên host, không do Docker quản lý
- Thay đổi real-time giữa host và container
- Phụ thuộc cấu trúc thư mục của host

**Anonymous Volume**

Anonymous volume được tạo tự động khi container cần volume nhưng không chỉ định tên.

Đặc điểm:
- Docker gán UUID làm tên, khó quản lý về sau
- Bị xóa khi dùng `docker compose down -v`

---

## 4. Cấu trúc Volume trong Compose File

Volume trong Compose được khai báo ở hai cấp:

- Service level: khai báo volume nào được gắn vào container này và tại đường dẫn nào trong container.

- Top-level: khai báo named volume để Compose tạo và quản lý. Volume khai báo ở đây có thể được dùng bởi nhiều service.

Nếu một service dùng named volume mà không khai báo ở top-level, Compose sẽ tự động tạo nhưng không có cấu hình rõ ràng.
