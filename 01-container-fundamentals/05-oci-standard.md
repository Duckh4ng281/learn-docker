# OCI Standard

## 1. Tổng quan

OCI (Open Container Initiative) là một tổ chức được thành lập nhằm xây dựng các tiêu chuẩn mở cho công nghệ container.

Mục tiêu của OCI là chuẩn hóa cách container image được build, cách container được chạy và cách image được phân phối, giúp đảm bảo tính tương thích giữa các hệ sinh thái container khác nhau.


---

## 2. Vấn đề trước khi có OCI

Trước khi OCI ra đời, tồn tại nhiều vấn đề:

- Mỗi nền tảng container có định dạng image riêng, không tương thích với nhau
- Runtime của Docker không thể dùng với các công cụ container khác
- Khó khăn khi chuyển đổi hoặc di chuyển container giữa các nền tảng

Tình trạng này dẫn đến sự phân mảnh trong hệ sinh thái container và hạn chế khả năng cạnh tranh, đổi mới.

---

## 3. Mục tiêu của OCI

OCI được thiết kế nhằm:

- Chuẩn hóa định dạng container image để image từ một công cụ có thể chạy trên công cụ khác
- Chuẩn hóa cách container runtime hoạt động để runtime có thể được thay thế linh hoạt
- Chuẩn hóa giao thức phân phối image giữa các registry
- Giảm sự phụ thuộc vào một công nghệ container duy nhất

---

## 4. Các tiêu chuẩn chính của OCI

OCI hiện duy trì ba tiêu chuẩn quan trọng:

**OCI Image Specification**

OCI Image Specification định nghĩa cấu trúc chuẩn của một container image. Nhờ tiêu chuẩn này, một image được build bằng Docker có thể chạy bằng bất kỳ runtime nào tuân thủ OCI.

**OCI Runtime Specification**

OCI Runtime Specification định nghĩa cách một container được tạo và thực thi trên hệ điều hành.

**OCI Distribution Specification**

OCI Distribution Specification định nghĩa cách thiết kế API chuẩn để push và pull image giữa client và registry. 
