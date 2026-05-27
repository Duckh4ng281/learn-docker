# Docker Compose Overview

## 1. Tổng quan

Docker Compose là công cụ giúp định nghĩa và chạy các ứng dụng nhiều container bằng một file cấu hình duy nhất (`docker-compose.yml`).

Thay vì quản lý từng container riêng lẻ bằng nhiều lệnh docker, Docker Compose cho phép mô tả toàn bộ hệ thống ứng dụng (container, network, volume, biến môi trường...) trong một cấu trúc thống nhất, sau đó khởi động hay dừng toàn bộ bằng một lệnh duy nhất.

---

## 2. Mục tiêu của Docker Compose

Docker Compose được thiết kế nhằm:

- Đơn giản hóa việc quản lý ứng dụng multi-container
- Chuẩn hóa cấu hình môi trường chạy ứng dụng dưới dạng file có thể version control
- Tăng khả năng tái tạo môi trường nhất quán trên nhiều máy khác nhau
- Giảm lỗi do cấu hình thủ công không nhất quán

---

## 3. Compose v1 và Compose v2

Có hai phiên bản Docker Compose với cú pháp lệnh khác nhau, đây là nguồn nhầm lẫn phổ biến:

**Compose v1**: công cụ riêng biệt viết bằng Python, gọi bằng lệnh `docker-compose` (có dấu gạch ngang). Không còn được phát triển.

**Compose v2**: được viết lại bằng Go, tích hợp trực tiếp vào Docker CLI dưới dạng plugin, gọi bằng lệnh `docker compose` (không có dấu gạch ngang). Đây là phiên bản hiện tại và được khuyến nghị sử dụng.

File `docker-compose.yml` tương thích với cả hai phiên bản.

---

## 4. Vai trò của Docker Compose

Docker Compose hoạt động như một lớp orchestration, giúp:

- Quản lý nhiều container như một đơn vị duy nhất
- Tự động tạo và kết nối network giữa các service
- Quản lý volume cho toàn bộ hệ thống
- Xác định thứ tự khởi động dựa trên dependencies giữa service

---

## 5. Compose và Docker Engine

Docker Compose không thay thế Docker Engine mà hoạt động như một client mở rộng bên trên.

Luồng hoạt động:

1. Docker Compose đọc và parse file cấu hình
2. Resolve biến môi trường và các giá trị động
3. Gửi request đến Docker Engine thông qua Docker API
4. Docker Engine tạo container, network và volume tương ứng
5. Compose theo dõi và quản lý lifecycle của toàn bộ stack

Mọi thứ Compose làm đều có thể thực hiện thủ công bằng Docker CLI, Compose chỉ tự động hóa và tổ chức các bước đó.