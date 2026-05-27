# Container là gì

## 1. Tổng quan

Container là một đơn vị đóng gói phần mềm bao gồm ứng dụng và toàn bộ các thành phần phụ thuộc như thư viện, runtime và file cấu hình. Mục tiêu của container là đảm bảo ứng dụng có thể chạy nhất quán trên nhiều môi trường khác nhau mà không phụ thuộc vào hệ thống bên ngoài.

Container không phải là một máy ảo. Thay vì tạo ra một hệ điều hành riêng, container chạy trực tiếp trên cùng kernel của hệ điều hành host và chỉ cô lập môi trường thực thi của ứng dụng.

---

## 2. Mục tiêu của container

Container được thiết kế nhằm giải quyết các vấn đề trong quá trình phát triển và triển khai phần mềm, đặc biệt là sự khác biệt giữa các môi trường.

Vấn đề phổ biến nhất là ứng dụng chạy tốt trên máy dev nhưng gặp lỗi trên server do khác biệt về phiên bản thư viện, cấu hình hệ thống hoặc biến môi trường.

Container giải quyết vấn đề này bằng cách đóng gói toàn bộ môi trường chạy cùng với ứng dụng, đảm bảo hành vi nhất quán trên mọi môi trường.

---

## 3. Đặc điểm của container

**Tính nhẹ (Lightweight)**

Container không bao gồm hệ điều hành riêng mà chỉ chứa ứng dụng và các phụ thuộc cần thiết. Do đó, kích thước nhỏ hơn và sử dụng ít tài nguyên hơn so với máy ảo. Một host có thể chạy nhiều container đồng thời mà không cần tài nguyên lớn như khi dùng máy ảo.

**Tính cô lập (Isolation)**

Mỗi container chạy trong một môi trường riêng biệt, được cô lập về tiến trình, hệ thống file và mạng. Điều này giúp các ứng dụng không ảnh hưởng lẫn nhau khi chạy trên cùng một host.

**Tính di động (Portability)**

Container có thể chạy nhất quán trên nhiều môi trường khác nhau, miễn là có container runtime tương thích. 

**Tốc độ khởi động nhanh**

Do không cần khởi động hệ điều hành, container có thể được tạo và chạy gần như ngay lập tức.

---


## 4. Cách container hoạt động

Container hoạt động dựa trên các tính năng của Linux kernel để tạo môi trường cô lập cho ứng dụng.

- **Namespace**: cô lập các tài nguyên hệ thống như process, network, filesystem... khiến mỗi container nhìn thấy một hệ thống riêng biệt
- **Cgroups**: giới hạn và quản lý tài nguyên như CPU, RAM, I/O... ngăn một container chiếm dụng toàn bộ tài nguyên hệ thống
- **Union filesystem**: tạo hệ thống file theo dạng nhiều lớp (layered filesystem), giúp chia sẻ dữ liệu hiệu quả giữa nhiều container

Nhờ sự kết hợp của các cơ chế này, container có thể hoạt động như một môi trường độc lập nhưng vẫn chia sẻ kernel của hệ điều hành host.
