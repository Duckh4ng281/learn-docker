# Environment Management

## 1. Tổng quan

Environment Management trong Docker Compose là cơ chế quản lý cấu hình môi trường cho các service thông qua biến môi trường.

Cơ chế này giúp tách cấu hình khỏi code, cho phép ứng dụng chạy linh hoạt trong nhiều môi trường khác nhau mà không cần thay đổi image hay Compose file.

---

## 2. Vai trò của Environment Management

Environment Management trong Compose giúp:

- Tách cấu hình khỏi source code
- Thay đổi cấu hình theo môi trường mà không rebuild image
- Giảm hard-code giá trị trong code và trong Compose file
- Hỗ trợ nhiều môi trường deploy với cùng một Compose file
- Tăng tính tái sử dụng của cấu hình

---

## 3. Các cách truyền biến môi trường

Docker Compose hỗ trợ nhiều cách truyền biến môi trường vào service:

**Khai báo trực tiếp trong Compose file**

Giá trị được viết thẳng vào file cấu hình. Phù hợp với các giá trị không nhạy cảm và không thay đổi giữa các môi trường.

**File `.env`**

File đặc biệt nằm cùng thư mục với Compose file, tự động được Docker Compose load khi khởi động. Giá trị trong file này được dùng để thay thế các biến trong Compose file và cũng có thể được truyền vào container.

**Biến môi trường từ host**

Compose có thể đọc giá trị từ biến môi trường của host đang chạy lệnh compose.

**Nhiều env file**

Compose hỗ trợ khai báo nhiều file env cho từng service thông qua thuộc tính `env_file`, thay vì phải khai báo từng biến một.

---

## 4. File .env trong Compose

File `.env` là cơ chế phổ biến nhất để quản lý environment variables trong Compose.

Đặc điểm:
- Nằm cùng thư mục với Compose file
- Tự động được Docker Compose load, không cần khai báo
