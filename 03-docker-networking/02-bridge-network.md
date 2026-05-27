# Bridge Network

## 1. Tổng quan

Bridge Network là loại network mặc định và phổ biến nhất trong Docker, cho phép các container trên cùng một host giao tiếp với nhau thông qua một virtual bridge.

Docker tạo ra một bridge interface trên host (`docker0`) đóng vai trò như một switch ảo kết nối các container trong cùng mạng.

---

## 2. Vai trò của Bridge Network

Bridge Network được sử dụng để:

- Kết nối các container trên cùng một host với nhau
- Cô lập mạng giữa các nhóm container khác nhau
- Cho phép container giao tiếp nội bộ mà không cần expose ra bên ngoài
- Cung cấp môi trường mạng mặc định khi tạo container

---

## 3. Hai loại Bridge Network

Docker có hai loại bridge network:

**Default bridge network (docker0)**

Là bridge được Docker tạo tự động khi cài đặt. Container được gắn vào bridge này nếu không chỉ định network khi chạy.

Hạn chế của default bridge: các container không thể giao tiếp với nhau qua tên container (không có DNS), chỉ có thể dùng IP nội bộ. IP này có thể thay đổi mỗi lần container restart.

**User-defined bridge network**

Là bridge do người dùng tạo thủ công.

- Hỗ trợ DNS resolution, container có thể giao tiếp qua tên container thay vì IP
- Cô lập tốt hơn, chỉ container trong cùng network mới giao tiếp được với nhau

---

## 4. Cơ chế hoạt động

Khi một container được gắn vào bridge network, Docker sẽ:

1. Tạo một veth pair
2. Gắn một đầu vào network namespace của container
3. Gắn đầu kia vào bridge interface trên host
4. Cấp IP nội bộ cho container trong subnet của bridge

Từ đó, các container trong cùng bridge network có thể giao tiếp trực tiếp với nhau thông qua IP hoặc qua tên container.
---

## 5. Cô lập giữa các Bridge Network

Mỗi bridge network là một không gian mạng riêng biệt.

Container thuộc bridge network này không thể giao tiếp trực tiếp với container thuộc bridge network khác.

Tính cô lập này cho phép chạy nhiều ứng dụng độc lập trên cùng host mà không lo xung đột dữ liệu giữa các ứng dụng.
