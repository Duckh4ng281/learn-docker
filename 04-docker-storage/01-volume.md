# Volume

## 1. Tổng quan

Volume là cơ chế lưu trữ dữ liệu trong Docker được thiết kế để tách dữ liệu ra khỏi vòng đời của container.

Volume được quản lý độc lập bởi Docker và lưu trữ trực tiếp trên host tại thư mục `/var/lib/docker/volumes/`. Dữ liệu trong volume tồn tại độc lập với container.

---

## 2. Vai trò của Volume

Volume được sử dụng để:

- Lưu trữ dữ liệu bền vững cần giữ lại sau khi container bị xóa hoặc khởi tạo lại
- Tách dữ liệu khỏi vòng đời container để quản lý độc lập
- Chia sẻ dữ liệu giữa nhiều container đang chạy
- Hỗ trợ backup, restore và migration dữ liệu

---

## 3. Cơ chế hoạt động

Khi volume được gắn vào container:

1. Docker tạo một thư mục trong `/var/lib/docker/volumes/<volume-name>/_data/` (nếu chưa tồn tại)
2. Volume được mount vào container tại đường dẫn được chỉ định
3. Container đọc/ghi dữ liệu trực tiếp vào volume như một thư mục bình thường
4. Dữ liệu được ghi thẳng xuống filesystem của host

---

## 4. Các loại Volume

**Named Volume**

Named volume là volume được đặt tên rõ ràng bởi người dùng. Docker lưu volume tại `/var/lib/docker/volumes/<tên>/`.

Dễ quản lý, có thể tái sử dụng giữa nhiều container.

**Anonymous Volume**

Anonymous volume được Docker tạo tự động khi container được chạy với volume không có tên. Docker gán cho nó một UUID dài làm tên.

Khó nhận biết volume thuộc container nào, dễ bị bỏ lại sau khi container bị xóa.


---

## 5. Vòng đời của Volume

Volume có vòng đời độc lập với container:

- Volume tồn tại ngay cả khi container bị xóa
- Volume không tự động bị xóa khi dừng hay xóa container
- Volume chỉ bị xóa khi người dùng xóa thủ công hoặc khi dùng lệnh prune để dọn dẹp volume không còn được dùng bởi container nào

Vòng đời độc lập này đảm bảo dữ liệu quan trọng không bị mất cùng với container.
