---
title: "Worklog Tuần 2"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

Trong Tuần 2 (21/09/2026–25/09/2026), tôi tìm hiểu compute, object storage, cơ sở dữ liệu được quản lý và so sánh các lựa chọn hosting và mở rộng.

### Mục tiêu tuần 2

- Hiểu compute (EC2, IAM Role cho EC2), object storage (S3) và cơ sở dữ liệu quan hệ được quản lý (RDS).
- So sánh dịch vụ hosting đơn giản, triển khai container và tự động mở rộng.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Tìm hiểu EC2 cơ bản và IAM Role cho EC2. | 21/09/2026 | 21/09/2026 | [Amazon EC2](https://000004.awsstudygroup.com/)<br>[IAM Roles for EC2](https://000048.awsstudygroup.com/) |
| 3 | Tìm hiểu công cụ phát triển cloud, object storage và cơ sở dữ liệu quan hệ được quản lý. | 22/09/2026 | 22/09/2026 | [AWS Cloud9](https://000049.awsstudygroup.com/)<br>[Amazon S3](https://000057.awsstudygroup.com/)<br>[Amazon RDS](https://000005.awsstudygroup.com/) |
| 4 | So sánh dịch vụ hosting đơn giản, triển khai container và tự động mở rộng. | 23/09/2026 | 23/09/2026 | [Amazon Lightsail](https://000045.awsstudygroup.com/)<br>[Lightsail Containers](https://000046.awsstudygroup.com/)<br>[EC2 Auto Scaling](https://000006.awsstudygroup.com/) |

### Kết quả đạt được

- Ôn tập EC2 instance, AMI, instance type, key pair, EBS, địa chỉ IP và các cách kết nối thông dụng.
- Hiểu security group và định tuyến subnet ảnh hưởng thế nào đến khả năng truy cập instance.
- Hiểu vì sao nên dùng instance profile/IAM Role để ứng dụng trên EC2 gọi AWS API thay vì lưu access key dài hạn trên máy chủ.
- Ghi nhớ lựa chọn tài nguyên vừa đủ và terminate instance không còn dùng để kiểm soát chi phí.
- Tìm hiểu mục đích của môi trường phát triển trên cloud và vai trò của công cụ dòng lệnh khi làm việc với AWS.
- Nắm khái niệm S3 bucket/object và các trường hợp lưu file hoặc tài nguyên tĩnh; quyền truy cập cần được cấu hình có chủ đích.
- So sánh cơ sở dữ liệu quan hệ được quản lý bởi Amazon RDS với việc tự vận hành database server, bao gồm backup, bảo trì và lựa chọn high availability.
- Liên hệ lựa chọn dịch vụ theo nhu cầu ứng dụng: S3 lưu object/file, RDS lưu dữ liệu quan hệ và compute chạy mã ứng dụng.
- So sánh cách triển khai đơn giản của Lightsail với việc tự cấu hình từng thành phần hạ tầng AWS.
- Tìm hiểu triển khai container trên Lightsail và lợi ích của việc đóng gói ứng dụng cùng các dependency runtime.
- Hiểu mục đích của EC2 Auto Scaling Group: duy trì số lượng instance mong muốn và điều chỉnh capacity theo policy hoặc health check.
- Tổng kết nền tảng tuần đầu gồm tài khoản và chi phí, quản lý quyền, networking, compute và storage.

### Bài học rút ra

- Role gắn với instance an toàn hơn việc lưu thông tin xác thực trên máy chủ.
- Nên chọn dịch vụ đơn giản nhất đáp ứng yêu cầu trước khi chuyển sang các dịch vụ linh hoạt hơn.

### Bước tiếp theo

Chuyển sang giám sát, DNS, NoSQL và làm quen Lambda ở Tuần 3.
