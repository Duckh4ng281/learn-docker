# Docker Architecture

## 1. Tổng quan

Docker Architecture là kiến trúc tổng thể của Docker, mô tả cách các thành phần phối hợp với nhau để xây dựng, quản lý và chạy container.

Docker được thiết kế theo mô hình client-server, trong đó các thành phần được tách biệt rõ ràng để đảm bảo tính linh hoạt, khả năng mở rộng và dễ quản lý.

---

## 2. Kiến trúc tổng thể của Docker

Docker bao gồm các thành phần chính:

- Docker Client: giao diện người dùng để gửi lệnh
- Docker Daemon: thành phần trung tâm xử lý mọi yêu cầu
- Docker API: cách thức thức giao tiếp giữa client và daemon
- Container Runtime: thực thi container ở mức hệ điều hành
- Docker Objects: các đối tượng Docker quản lý (Image, Container, Network, Volume...)


---

## 3. Mô hình Client — Server

**Docker Engine**

Docker Engine là thành phần cốt lõi bao gồm Docker Daemon, API và các thành phần runtime cần thiết để chạy container. Docker Engine đóng vai trò là lớp điều phối giữa người dùng và hệ điều hành host.

**Docker Client**

Docker Client là giao diện người dùng để tương tác với Docker. Khi chạy các lệnh `docker`, Docker Client nhận lệnh đó, chuyển thành API request và gửi đến Docker Daemon.

Client không trực tiếp xử lý container mà chỉ đóng vai trò trung gian giao tiếp. Client và Daemon có thể chạy trên cùng một máy hoặc khác máy.

**Docker Daemon**

Docker Daemon (dockerd) là thành phần trung tâm của Docker, chạy liên tục ở nền.

Daemon chịu trách nhiệm:

- Nhận và xử lý request từ Docker Client
- Quản lý lifecycle của container
- Quản lý image
- Quản lý các đối tượng tròn Docker
- Giao tiếp với container runtime


**Docker API**

Docker API cho phép Docker Client giao tiếp với Docker Daemon. Mọi thao tác từ client đều được chuyển thành API request. Docker API cũng cho phép các công cụ bên ngoài tích hợp trực tiếp với Docker.

---

## 4. Docker Objects

Docker quản lý bốn loại object chính:

**Image**

Image là template chỉ đọc dùng để tạo container. Image được xây dựng từ Dockerfile và lưu trữ dưới dạng các layer. Một image có thể được dùng để tạo nhiều container.

**Container**

Container là instance đang chạy được tạo từ image. Container có thêm một container layer phía trên image để ghi dữ liệu trong runtime. Khi container bị xóa, container layer cũng mất theo.

**Network**

Network cung cấp cơ chế kết nối giữa các container với nhau và với hệ thống bên ngoài.

**Volume**

Volume cung cấp cơ chế lưu trữ dữ liệu bền vững, tồn tại độc lập với vòng đời của container, dữ liệu trong volume vẫn còn sau khi container bị xóa.

---

## 5. Luồng hoạt động của Docker

Khi người dùng chạy lệnh tạo container trong Docker, quy trình diễn ra như sau:

1. Docker Client nhận lệnh từ người dùng
2. Client chuyển lệnh thành API request và gửi đến Docker Daemon
3. Docker Daemon nhận request và xử lý logic
4. Daemon gọi containerd (high-level runtime) để quản lý container
5. containerd gọi runc (low-level runtime) để tạo process container
6. runc tương tác với Linux kernel thông qua namespace và cgroups
7. Container được khởi chạy và Docker Daemon theo dõi trạng thái

---

## 6. Giao tiếp giữa các thành phần

Docker Client giao tiếp với Docker Daemon thông qua Unix socket trên cùng máy hoặc qua TCP khi client và daemon ở xa nhau.

Docker Daemon giao tiếp với containerd thông qua gRPC. containerd giao tiếp với runc thông qua OCI bundle trên filesystem.

Kiến trúc phân tầng này giúp mỗi thành phần có thể được nâng cấp hoặc thay thế độc lập mà không ảnh hưởng đến toàn hệ thống.
