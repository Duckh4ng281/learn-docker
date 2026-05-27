# Swarm Deploy

## 1. Tổng quan

Deploy trong Docker Swarm là quá trình đưa ứng dụng lên cluster dưới dạng Stack, một nhóm service liên quan được định nghĩa trong một file YAML và quản lý cùng nhau như một đơn vị.

Stack trong Swarm tương đương với project trong Docker Compose, nhưng được triển khai trên cluster thay vì đơn host.

---

## 2. Stack và Docker Stack Deploy

Docker Swarm sử dụng file Compose để định nghĩa stack.

Khi deploy một stack:
- Swarm đọc file cấu hình và tạo các service được khai báo
- Mỗi service được phân phối task đến các node phù hợp
- Network và volume được tạo tự động
- Toàn bộ stack được quản lý dưới một tên duy nhất

Stack có thể được cập nhật bằng cách chạy lại lệnh deploy với file cấu hình mới, Swarm tự động xác định service nào thay đổi và chỉ cập nhật service đó.

---


## 3. Placement Constraint và Preference

**Placement constraint** là điều kiện bắt buộc, task chỉ được lập lịch trên node thỏa mãn constraint.

**Placement preference** là gợi ý ưu tiên phân phối task (không bắt buộc).

---

## 4. Resource Constraints

Swarm cho phép đặt giới hạn tài nguyên cho từng service:

**Limits**: mức tài nguyên tối đa mà task được phép dùng. Nếu vượt quá giới hạn CPU, task bị throttle. Nếu vượt quá giới hạn memory, task bị OOM kill.

**Reservations**: tài nguyên được đảm bảo cho task. Swarm chỉ lập lịch task trên node có đủ tài nguyên reserved. Đây là cơ sở để Swarm quyết định node nào còn đủ tài nguyên để nhận task mới.

Đặt cả limits và reservations giúp Swarm lập lịch hiệu quả và ngăn một task chiếm quá nhiều tài nguyên của node.

---

## 5. Restart Policy

Restart policy xác định hành vi khi task thất bại:

- **condition**: khi nào restart (`none`, `on-failure`, `any`)
- **delay**: thời gian chờ giữa các lần restart
- **max_attempts**: số lần restart tối đa trong window
- **window**: khoảng thời gian để đánh giá số lần restart
