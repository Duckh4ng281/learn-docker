# Docker Image

## 1. Tổng quan

Docker Image là một bản đóng gói chứa toàn bộ các thành phần cần thiết để chạy ứng dụng như source code, thư viện, runtime và cấu hình.

Trong Docker, image đóng vai trò là template để tạo ra container. Mỗi container được khởi chạy đều bắt đầu từ một image cụ thể.

---

## 2. Image và container

Image và container có mối quan hệ một chiều:

- Image là trạng thái static — template bất biến
- Container là trạng thái runtime — instance đang chạy từ template đó

Một image có thể được dùng để tạo nhiều container chạy đồng thời, mỗi container hoạt động độc lập. Bản thân image không thay đổi trong quá trình container chạy.

---

## 3. Cấu trúc layer của Docker image

Docker image được xây dựng theo mô hình layer. Các layer này được xếp chồng lên nhau để tạo thành một filesystem hoàn chỉnh mà container sử dụng khi khởi chạy.

Mỗi layer tương ứng với một lệnh trong Dockerfile. Layer chỉ lưu trữ phần thay đổi so với layer phía dưới.

---

## 4. Đặc điểm của image layer

Các layer của Docker image có những đặc điểm:

- **Chỉ đọc (read-only)**: layer không thay đổi sau khi được tạo, đảm bảo tính bất biến của image
- **Tái sử dụng**: nhiều image có thể chia sẻ các layer giống nhau, tiết kiệm dung lượng lưu trữ trên disk và băng thông khi pull/push
- **Content-addressable storage**: mỗi layer được định danh bằng hash SHA256 của nội dung, cùng nội dung sẽ có cùng hash và không bao giờ bị lưu trùng lặp

---

## 5. Image manifest

Khi image được lưu trữ trong registry, nó kèm theo một manifest — một file JSON mô tả cấu trúc của image.

Manifest chứa:
- Danh sách các layer theo thứ tự, cùng với hash và kích thước của từng layer
- Image config (entrypoint, cmd, environment variables, exposed ports, labels...)
- Media type và schema version

Manifest là tài liệu tham chiếu mà Docker dùng để tải đúng các layer khi pull image và để xác minh tính toàn vẹn của image sau khi tải về.

---

## 6. Tính bất biến của image

Docker image có tính bất biến, nghĩa là sau khi được tạo ra thì không thể chỉnh sửa trực tiếp.

Nếu cần thay đổi ứng dụng hay cấu hình, phải build một image mới thay vì chỉnh sửa image cũ.

---

## 7. Multi-platform image

Docker image có thể được build cho nhiều kiến trúc CPU khác nhau như `linux/amd64`, `linux/arm64`...

Multi-platform image sử dụng cơ chế image index — một manifest tổng hợp trỏ đến các manifest riêng cho từng platform. Khi pull image, Docker tự động chọn đúng phiên bản phù hợp với kiến trúc của máy host mà không cần chỉ định thủ công.

---

## 8. Phân phối image

Docker image được chia sẻ thông qua registry. Quy trình phân phối:

1. Image được build từ Dockerfile
2. Image được push lên Docker registry
3. Các hệ thống khác pull image về và tạo container

Nhờ cơ chế layer và content-addressable storage, chỉ những layer chưa có trên máy đích mới cần được tải về, giúp quá trình pull nhanh hơn đáng kể.
