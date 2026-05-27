# Docker Container

## 1. Tổng quan

Trong Docker, container là một runtime object được tạo ra từ image và được quản lý bởi Docker Daemon.

Container đại diện cho một phiên chạy của ứng dụng. Docker quản lý toàn bộ vòng đời container từ khởi tạo, thực thi đến dừng và xóa thông qua Docker Daemon.

---

## 2. Container lifecycle trong Docker

Docker quản lý container theo một tập các trạng thái xác định:

| Trạng thái | Mô tả |
|------------|-------|
| **Created** | Container đã được tạo từ image nhưng chưa khởi chạy. |
| **Running** | Container đang thực thi process chính. |
| **Paused** | Container bị tạm dừng, process bị đóng băng nhưng vẫn còn trong memory. |
| **Stopped** | Process chính đã kết thúc. Container vẫn tồn tại trên disk nhưng không chạy. |
| **Removed** | Container bị xóa hoàn toàn, bao gồm cả container layer. |

Docker Daemon chịu trách nhiệm chuyển đổi giữa các trạng thái này khi nhận lệnh từ người dùng hoặc khi process tự kết thúc.

---

## 3. Container gắn liền với process chính

Mỗi container tương ứng với một process chính. Docker theo dõi process này để xác định trạng thái container:

- Nếu process đang chạy → container ở trạng thái Running
- Nếu process kết thúc → container tự chuyển sang trạng thái Stopped
- Exit code của process chính là exit code của container

---

## 5. Container layer của Docker container

Khi container được tạo từ image, Docker thêm một container layer phía trên các image layer. Đây là lớp duy nhất cho phép ghi dữ liệu trong runtime.

Container layer có đặc điểm:
- Tồn tại cùng vòng đời của container
- Bị xóa khi container bị xóa
- Không chia sẻ với container khác
- Sử dụng cơ chế copy-on-write

Dữ liệu cần giữ lại sau khi container bị xóa phải được lưu vào volume hoặc bind mount, không phải vào container layer.

---

## 6. Docker quản lý container như thế nào

Docker không trực tiếp chạy container ở mức kernel. Thay vào đó:

1. Docker Daemon nhận yêu cầu tạo container từ client
2. Daemon chuẩn bị cấu hình và gửi xuống containerd
3. containerd giải nén image, tạo OCI bundle và gọi runc
4. runc tạo process thực thi với namespace và cgroups đã cấu hình
5. Docker Daemon theo dõi trạng thái process và cập nhật metadata container

Docker đóng vai trò là orchestration layer, chịu trách nhiệm quản lý toàn bộ vòng đời và cung cấp giao diện nhất quán cho người dùng.
