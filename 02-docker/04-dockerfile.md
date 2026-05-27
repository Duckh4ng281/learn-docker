# Dockerfile

## 1. Tổng quan

Dockerfile là một file văn bản chứa tập hợp các instruction dùng để tự động build Docker image.

Dockerfile đóng vai trò như bản thiết kế của image, giúp chuẩn hóa quá trình build và đảm bảo image có thể được tái tạo nhất quán trên nhiều môi trường khác nhau. Dockerfile thường được lưu cùng source code trong version control để mọi thay đổi về môi trường đều có thể truy vết.

---

## 2. Cách Dockerfile hoạt động

Dockerfile được xử lý tuần tự từ trên xuống dưới. Mỗi instruction gây thay đổi filesystem sẽ tạo ra một layer mới trong image. Kết quả là một Docker image hoàn chỉnh với nhiều layer xếp chồng lên nhau.

---

## 3. Các instruction phổ biến

**FROM**

Khai báo base image để bắt đầu build. Mọi Dockerfile phải bắt đầu bằng `FROM`. Base image xác định hệ điều hành và runtime nền tảng.

**RUN**

Thực thi lệnh shell trong quá trình build. Thường dùng để cài đặt package, tạo thư mục hoặc thiết lập cấu hình.

**COPY / ADD**

Sao chép file từ build context vào image. `COPY` sao chép file. `ADD` có thêm khả năng giải nén archive và tải file từ URL.

**ENV**

Đặt biến môi trường trong image. Biến này có hiệu lực trong cả quá trình build (từ instruction đó trở xuống) lẫn trong container runtime.

**WORKDIR**

Đặt thư mục làm việc cho các instruction tiếp theo và cho container khi chạy.

**EXPOSE**

Khai báo port mà ứng dụng trong container lắng nghe. Đây chỉ là metadata, không tự động mở port.

**CMD / ENTRYPOINT**

Định nghĩa process chính của container khi khởi chạy. `ENTRYPOINT` là lệnh cố định; `CMD` là tham số mặc định có thể bị ghi đè khi chạy container.

---

## 4. Dockerfile và image layer

Mỗi instruction tạo thay đổi filesystem (như `RUN`, `COPY`, `ADD`) tạo ra một image layer mới. Các instruction không thay đổi filesystem (như `ENV`, `EXPOSE`, `WORKDIR`) được lưu vào metadata của image, không tạo layer riêng.

Các layer có đặc điểm:
- Được tạo tuần tự trong quá trình build
- Được tái sử dụng giữa các image có cùng layer nội dung
- Được định danh bằng hash SHA256 của nội dung

---

## 5. Build cache

Docker sử dụng build cache để tối ưu quá trình build image.

Khi build lại một Dockerfile, Docker kiểm tra từng instruction xem có thể dùng lại layer cũ không. Nếu instruction và context không thay đổi, Docker dùng layer đã cache thay vì build lại.

Tuy nhiên, nếu một instruction thay đổi, tất cả layer phía sau đó phải build lại vì mỗi layer phụ thuộc vào layer phía trước. Vì vậy, khi viết Dockerfile nên đặt các instruction ít thay đổi ở trên, các instruction thay đổi thường xuyên ở dưới.

---

## 6. Multi-stage build

Multi-stage build là kỹ thuật sử dụng nhiều stage (nhiều `FROM`)trong cùng một Dockerfile để tối ưu image cuối cùng.

Quá trình build ứng dụng cần nhiều công cụ như nhưng môi trường runtime chỉ cần ứng dụng đã được build, không cần các công cụ đó.

Multi-stage build giải quyết bằng cách:

- Build stage: dùng image đầy đủ công cụ để build ứng dụng
- Runtime stage: dùng image tối giản, chỉ copy artifact cần thiết từ build stage
