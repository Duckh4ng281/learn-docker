# Container Runtime

## 1. Tổng quan

Container runtime là thành phần chịu trách nhiệm trực tiếp tạo, chạy và quản lý vòng đời của container trên hệ điều hành host.

Runtime là lớp trung gian giữa container engine và Linux kernel, giúp chuyển đổi các yêu cầu tạo container thành các thao tác cụ thể ở mức hệ điều hành.

---

## 2. Vai trò của container runtime

Container runtime đảm nhận các nhiệm vụ chính:

- Tạo và khởi chạy container từ image
- Quản lý vòng đời container
- Thiết lập namespace để cô lập môi trường thực thi
- Áp dụng cgroups để giới hạn tài nguyên
- Chuẩn bị filesystem cho container từ các image layer

Runtime là thành phần trực tiếp biến container thành một process đang chạy trên host.

---

## 3. Phân tầng container runtime

Container runtime thường được chia thành hai lớp chính với trách nhiệm khác nhau:

**High-level runtime**

High-level runtime chịu trách nhiệm quản lý vòng đời container và cung cấp API cho container engine. Nó không trực tiếp tạo container mà đóng vai trò điều phối giữa engine và low-level runtime.

Ví dụ: containerd

Đặc điểm:
- Quản lý image
- Quản lý container lifecycle
- Giao tiếp với low-level runtime
- Cung cấp API cho container engine

**Low-level runtime**

Low-level runtime chịu trách nhiệm tạo process container thực sự bằng cách gọi trực tiếp vào Linux kernel.

Ví dụ: runc

Đặc điểm:
- Thiết lập namespace và cgroups
- Mount filesystem cho container
- Thực thi process đầu tiên trong container
- Thoát sau khi container đã được tạo

---

## 4. Cách container runtime hoạt động

Quá trình tạo container diễn ra theo các bước:

1. Container engine gửi yêu cầu tạo container đến high-level runtime
2. High-level runtime giải nén image, chuẩn bị filesystem và tạo OCI bundle
3. High-level runtime gọi low-level runtime với OCI bundle
4. Low-level runtime gọi Linux kernel để tạo namespace và cgroups
5. Low-level runtime mount filesystem và khởi chạy process đầu tiên trong container
6. Container bắt đầu chạy như một process độc lập, được cô lập bởi namespace

---

## 5. Mối quan hệ giữa Docker và runtime

Docker không trực tiếp tạo container ở mức kernel. Docker Engine đóng vai trò như một lớp quản lý cấp cao, sử dụng container runtime bên dưới để thực thi container.

Luồng hoạt động:

```
Docker Client → Docker Daemon → containerd → runc → Linux kernel
```

Cấu trúc phân tầng này cho phép thay thế từng thành phần độc lập.