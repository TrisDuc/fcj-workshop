---
title: "Worklog Tuần 6"
date: 2026-10-19
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

Trong Tuần 6 (19/10/2026–23/10/2026), tôi học backup, VPC peering, nhắn tin với SQS và SNS và nền tảng container với Docker và ECS.

### Mục tiêu tuần 6

- Hiểu backup, VPC peering và nhắn tin hướng sự kiện với SQS, SNS (hàng đợi, retry, dead-letter queue).
- Nắm kiến thức nền tảng về container: Docker, ECS và triển khai ECS bằng CDK.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Tìm hiểu backup, VPC Peering và messaging theo sự kiện với SQS/SNS. | 19/10/2026 | 19/10/2026 | [AWS Backup](https://000013.awsstudygroup.com/)<br>[VPC Peering](https://000019.awsstudygroup.com/)<br>[SQS and SNS](https://000077.awsstudygroup.com/) |
| 3 | Tìm hiểu chia sẻ EBS và vận hành có tính sẵn sàng cao. | 20/10/2026 | 20/10/2026 | [EBS Multi-Attach](https://100000.awsstudygroup.com/)<br>[EBS Multi-Attach and Systems Manager](https://100001.awsstudygroup.com/) |
| 4 | Tìm hiểu Docker, ECS và triển khai hạ tầng ECS bằng CDK. | 21/10/2026 | 21/10/2026 | [Docker](https://000015.awsstudygroup.com/)<br>[Amazon ECS](https://000016.awsstudygroup.com/)<br>[ECS with CDK](https://000118.awsstudygroup.com/) |

### Kết quả đạt được

- Tìm hiểu AWS Backup như cách tập trung cấu hình backup plan, lịch chạy và thời gian lưu giữ cho các tài nguyên được hỗ trợ.
- Hiểu VPC Peering kết nối hai VPC qua định tuyến IP riêng nhưng không có tính transitive; route table và security rule vẫn quyết định khả năng kết nối.
- So sánh SQS queue để xử lý bất đồng bộ point-to-point với SNS topic để phát thông điệp theo mô hình publish/subscribe, bao gồm pattern SNS-to-SQS.
- Nhận biết vai trò của retry, visibility timeout, dead-letter queue, consumer idempotent và monitoring trong quy trình bất đồng bộ tin cậy.
- Hiểu mục đích và giới hạn của EBS Multi-Attach đối với volume io1/io2 được hỗ trợ và EC2 instance tương thích trong cùng Availability Zone.
- Ôn tập rằng gắn chung block device không tự đảm bảo ghi đồng thời an toàn; ứng dụng và cluster filesystem phải phối hợp truy cập đúng cách.
- Tìm hiểu cách Systems Manager hỗ trợ các tác vụ vận hành và quản lý instance trong mô hình shared storage.
- Phân biệt shared storage với replication database, backup và khôi phục đa AZ vì chúng giải quyết những bài toán availability và bảo vệ dữ liệu khác nhau.
- Ôn tập container image, container, Dockerfile, image registry và lợi ích của đóng gói nhất quán giữa các môi trường.
- Hiểu khái niệm cốt lõi của ECS như cluster, task definition, task, service và mối quan hệ giữa image với workload đang chạy.
- So sánh triển khai hạ tầng ECS thủ công với việc định nghĩa tài nguyên bằng CDK.
- Tổng hợp lưu ý vận hành và bảo mật như nguồn gốc image, cấu hình môi trường, logs, health check, capacity và dọn dẹp tài nguyên.

### Bài học rút ra

- Hàng đợi tách rời các thành phần, nhưng consumer phải xử lý retry và message trùng.
- Dead-letter queue biến lỗi âm thầm thành thứ nhìn thấy được.

### Bước tiếp theo

Xây nền tảng serverless và CI/CD ở Tuần 7.
