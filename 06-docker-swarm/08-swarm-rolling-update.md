# Rolling Update

## 1. Tổng quan

Rolling Update là cơ chế cập nhật service trong Docker Swarm mà không gây downtime, bằng cách thay thế dần từng task cũ bằng task mới theo một lịch trình có kiểm soát.

Thay vì dừng toàn bộ service và khởi động lại, rolling update cập nhật từng batch nhỏ replica, đảm bảo luôn có một phần replica cũ đang chạy và phục vụ traffic trong suốt quá trình cập nhật.

---

## 2. Quy trình Rolling Update

Khi một service được update:

1. Swarm xác định số task cần update trong mỗi batch
2. Với mỗi batch: dừng task cũ, tạo task mới với cấu hình mới
3. Chờ task mới đạt trạng thái Running trong thời gian `update_delay`
4. Nếu task mới thành công: tiếp tục batch tiếp theo
5. Nếu task mới thất bại: thực hiện `failure_action` (pause hoặc rollback)
6. Lặp lại cho đến khi tất cả task được cập nhật

## 3. Rollback

Rollback là quá trình đưa service về phiên bản trước khi update. Swarm lưu cấu hình của phiên bản trước để có thể rollback khi cần.

Rollback có thể được kích hoạt:
- Tự động: khi `failure_action: rollback` và task mới thất bại liên tục
- Thủ công: người dùng chủ động rollback sau khi phát hiện vấn đề với phiên bản mới

Rollback cũng là một rolling update, nó thay thế dần task mới bằng task cũ.

Swarm chỉ lưu một phiên bản trước duy nhất. Không thể rollback về phiên bản trước đó nữa.
