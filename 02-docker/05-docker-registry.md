# Docker Registry

## 1. Tổng quan

Docker Registry là hệ thống dùng để lưu trữ, quản lý và phân phối Docker image.

Registry đóng vai trò là trung tâm phân phối, nơi image được push lên sau khi build và được pull về để triển khai trên các môi trường khác nhau.

---

## 2. Vai trò của Docker registry

Docker Registry có các vai trò chính:

- Lưu trữ Docker image theo từng repository
- Quản lý phiên bản image thông qua tag và digest
- Phân phối image đến nhiều hệ thống khác nhau
- Kiểm soát quyền truy cập theo người dùng hoặc tổ chức
- Quét bảo mật image

---

## 3. Cấu trúc trong Docker registry

Trong Docker registry, image được tổ chức theo cấu trúc phân cấp:

```
Registry
  └── Repository
        └── Image (:tag/:digest)
```

**Repository**

Repository là tập hợp các image liên quan đến cùng một ứng dụng hoặc service.

**Tag**

Tag là nhãn gắn vào một image để đánh dấu phiên bản, ví dụ: `latest`, `v1.0`... Tag là có thể bị cập nhật để trỏ sang một image khác mà không thay đổi tên tag.

**Digest**

Digest là định danh bất biến dựa trên hash SHA256 của manifest image.

---

## 4. Các loại Docker registry

**Public registry**

Public registry là registry công khai, cho phép bất kỳ ai truy cập và pull image. Docker Hub là public registry phổ biến nhất.

**Private registry**

Private registry là registry được triển khai riêng trong tổ chức hoặc doanh nghiệp, yêu cầu xác thực để truy cập.

---

## 5. Image versioning trong registry

Docker sử dụng hai cơ chế để quản lý phiên bản image:

- Versioning bằng tag: là cách phổ biến nhất, dễ đọc và dễ nhớ. Tuy nhiên tag có thể thay đổi theo thời gian, khiến cùng một lệnh pull có thể trả về image khác nhau ở các thời điểm khác nhau

- Versioning bằng digest: đảm bảo tính bất biến tuyệt đối khi pull image
