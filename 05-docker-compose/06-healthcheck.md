# Healthcheck

## 1. Tổng quan

Healthcheck trong Docker Compose là cơ chế dùng để kiểm tra định kỳ xem ứng dụng bên trong container có thực sự hoạt động đúng hay không.

Healthcheck không chỉ kiểm tra container đang "running", một container có thể đang chạy nhưng ứng dụng bên trong đã không còn phản hồi. Healthcheck phát hiện tình huống này bằng cách chạy một lệnh kiểm tra cụ thể và đánh giá kết quả.

---

## 2. Vai trò của Healthcheck

Healthcheck được sử dụng để:

- Xác định trạng thái thực tế của service, không chỉ trạng thái container
- Phát hiện service bị lỗi dù container vẫn đang chạy
- Hỗ trợ orchestration trong việc quyết định restart hay failover
- Đảm bảo dependency giữa service hoạt động đúng thứ tự

---

## 3. Các trạng thái Health của Container

Docker định nghĩa bốn trạng thái health:

| Trạng thái | Mô tả |
|-----------|-------|
| **starting** | Container vừa khởi động, healthcheck chưa chạy lần nào hoặc chưa đủ số lần để kết luận |
| **healthy** | Lệnh kiểm tra trả về exit code 0, service hoạt động bình thường |
| **unhealthy** | Lệnh kiểm tra thất bại liên tiếp đủ số lần retries, service có vấn đề |
| **none** | Không có healthcheck nào được cấu hình |

---

## 4. Cơ chế hoạt động

Healthcheck hoạt động theo chu kỳ:

1. Docker thực thi lệnh kiểm tra bên trong container theo interval đã cấu hình
2. Lệnh trả về exit code 0 → lần kiểm tra thành công
3. Lệnh trả về exit code khác 0 hoặc timeout → lần kiểm tra thất bại
4. Sau khi thất bại liên tiếp đủ số lần `retries`, container bị đánh dấu `unhealthy`
5. Một lần thành công duy nhất sẽ reset trạng thái về `healthy`

---

## 5. Các tham số Healthcheck

Mỗi healthcheck được cấu hình bằng các tham số:

| Tham số | Ý nghĩa | Mặc định |
|---------|---------|---------|
| `test` | Lệnh thực thi để kiểm tra | (bắt buộc) |
| `interval` | Khoảng thời gian giữa các lần kiểm tra | 30s |
| `timeout` | Thời gian tối đa cho mỗi lần kiểm tra | 30s |
| `retries` | Số lần thất bại liên tiếp trước khi unhealthy | 3 |
| `start_period` | Thời gian chờ sau khi container start trước khi bắt đầu đếm thất bại | 0s |

`start_period` hữu ích cho các service khởi động chậm, trong thời gian này healthcheck vẫn chạy nhưng thất bại không được tính vào `retries`.

---

## 6. Healthcheck và Dependency

Nếu chỉ dùng `depends_on` đơn giản, service phụ thuộc sẽ khởi động ngay khi container của dependency ở trạng thái `running` nhưng ứng dụng bên trong có thể chưa sẵn sàng.

Khi dùng `condition: service_healthy`, Compose chờ container dependency đạt trạng thái `healthy` trước khi khởi động service phụ thuộc.

---

## 7. Lợi ích của Healthcheck

- Phát hiện lỗi runtime sớm trước khi ảnh hưởng đến user
- Tăng độ ổn định hệ thống nhờ phát hiện tự động
- Hỗ trợ quyết định restart policy: với `restart: on-failure` và healthcheck, Docker có thể phân biệt container crash và container unhealthy