# Container Filesystem

## 1. Tổng quan

Container filesystem là hệ thống file mà container sử dụng để lưu trữ và truy cập dữ liệu trong quá trình hoạt động.

Filesystem của container không phải là một hệ thống độc lập hoàn toàn, mà được xây dựng từ nhiều layer dựa trên cơ chế union filesystem của Linux.

---

## 2. Đặc điểm của container filesystem

Container filesystem có các đặc điểm chính:

- Mỗi container có một filesystem riêng biệt, cô lập với các container khác
- Filesystem được xây dựng từ nhiều layer xếp chồng lên nhau
- Các layer từ image là read-only (image layer) và không thay đổi trong suốt vòng đời container
- Container có thêm một lớp ghi (container layer) trên cùng khi chạy
- Nhiều container có thể chia sẻ cùng image layer, tiết kiệm dung lượng lưu trữ

---

## 3. Cấu trúc filesystem của container

Filesystem của container được chia thành hai phần chính:

**Image layer (lớp chỉ đọc)**

Các layer được tạo trong quá trình build image. Mỗi layer đại diện cho một thay đổi trên filesystem như cài đặt package, thêm file hoặc chỉnh sửa cấu hình. Các layer này không thay đổi khi container đang chạy và có thể được chia sẻ giữa nhiều container khởi chạy từ cùng một image.

**Container layer (lớp ghi)**

Khi container được khởi chạy từ image, Docker tạo thêm một lớp ghi phía trên các image layer. Mọi thay đổi trong quá trình chạy như tạo file, sửa file hoặc xóa file đều được ghi vào lớp này. Khi container bị xóa, lớp ghi này cũng bị xóa theo, dẫn đến mất toàn bộ dữ liệu ghi trong quá trình chạy nếu không có cơ chế lưu trữ bên ngoài.

---

## 4. Union Filesystem

Container filesystem hoạt động dựa trên Union Filesystem.

Union filesystem cho phép kết hợp nhiều filesystem layer thành một filesystem duy nhất mà hệ điều hành và ứng dụng có thể nhìn thấy và sử dụng bình thường.

Khi container truy cập một file, hệ thống sẽ tìm kiếm từ trên xuống dưới theo thứ tự:

1. Kiểm tra container layer trên cùng trước
2. Nếu không có, tìm trong image layer phía dưới theo thứ tự từ trên xuống dưới
3. Trả về file từ layer đầu tiên tìm thấy

Hiện tại Docker chủ yếu sử dụng OverlayFS (cụ thể là overlay2) làm storage driver mặc định.

---

## 5. Copy-on-Write

Container sử dụng cơ chế Copy-on-Write để tối ưu hiệu năng và dung lượng.

Khi một file trong image layer cần được chỉnh sửa bởi container:

1. File gốc trong image layer không bị thay đổi
2. Một bản sao của file được tạo trong container layer
3. Các thay đổi chỉ xảy ra trên bản sao này
4. Các lần đọc tiếp theo sẽ thấy bản sao đã sửa đổi thay vì file gốc

Cơ chế này giúp image luôn giữ tính bất biến và có thể được tái sử dụng bởi nhiều container mà không cần sao chép toàn bộ dữ liệu.

---

## 6. Vòng đời filesystem của container

Filesystem của container tồn tại cùng vòng đời của container:

- Khi container bị xóa, container layer cũng bị xóa theo
- Các image layer vẫn được giữ nguyên trên host và tái sử dụng cho container khác
- Dữ liệu không bền vững nếu không sử dụng volume hoặc bind mount

Đây là lý do dữ liệu quan trọng cần được lưu trữ ngoài container filesystem thông qua volume hoặc bind mount.
