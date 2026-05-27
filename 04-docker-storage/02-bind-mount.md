# Bind Mount

## 1. Tổng quan

Bind Mount là cơ chế trong Docker cho phép gắn trực tiếp một file hoặc thư mục từ host vào container.

Khác với volume được quản lý bởi Docker, bind mount sử dụng trực tiếp một đường dẫn cụ thể trên filesystem của host. Người dùng kiểm soát hoàn toàn vị trí và nội dung dữ liệu.

---

## 2. Vai trò của Bind Mount

Bind Mount được sử dụng để:

- Chia sẻ source code giữa host và container trong quá trình phát triển
- Thêm file cấu hình từ host vào container
- Đọc/ghi log file từ container ra một thư mục cụ thể trên host
- Đồng bộ dữ liệu theo thời gian thực giữa host và container

---

## 3. Cơ chế hoạt động

Khi sử dụng bind mount:

1. Docker ánh xạ đường dẫn trên host vào một điểm mount trong container
2. Container truy cập đường dẫn đó như một thư mục bình thường
3. Mọi thay đổi ở host sẽ xuất hiện ngay lập tức trong container và ngược lại

---

## 4. Đặc điểm của Bind Mount

- Phụ thuộc đường dẫn host: bind mount gắn với một đường dẫn tuyệt đối trên host, nên cần đường dẫn đó tồn tại và đúng trên mọi máy chạy container
- Không có quản lý từ Docker: Docker không theo dõi, không backup và không quản lý lifecycle của dữ liệu trong bind mount
- Thay đổi real-time: thay đổi trên host được phản ánh ngay trong container
- Quyền truy cập: container có thể ghi lên filesystem của host, bao gồm cả các thư mục nhạy cảm nếu bind mount được cấu hình không cẩn thận
