---
title: "Worklog Tuần 3"
date: 2026-09-28
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

Trong Tuần 3 (28/09/2026–02/10/2026), tôi học giám sát, DNS, thao tác bằng CLI, NoSQL, caching và phân phối nội dung, đồng thời làm quen AWS Lambda lần đầu.

### Mục tiêu tuần 3

- Tìm hiểu giám sát, DNS, thao tác bằng CLI, cơ sở dữ liệu NoSQL, caching và phân phối nội dung.
- Làm quen AWS Lambda và giám sát nâng cao — những khối kiến trúc sẽ dùng lại trong dự án AskFirst.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Tìm hiểu CloudWatch, Route 53 và AWS CLI. | 28/09/2026 | 28/09/2026 | [Amazon CloudWatch](https://000008.awsstudygroup.com/)<br>[Amazon Route 53](https://000010.awsstudygroup.com/)<br>[AWS CLI](https://000011.awsstudygroup.com/) |
| 3 | Tìm hiểu DynamoDB, ElastiCache và CloudFront. | 29/09/2026 | 29/09/2026 | [Amazon DynamoDB](https://000060.awsstudygroup.com/)<br>[Amazon ElastiCache](https://000061.awsstudygroup.com/)<br>[Amazon CloudFront](https://000094.awsstudygroup.com/) |
| 4 | Tìm hiểu networking trên AWS và edge compute. | 30/09/2026 | 30/09/2026 | [Networking on AWS Workshop](https://000092.awsstudygroup.com/)<br>[CloudFront and Lambda@Edge](https://000130.awsstudygroup.com/) |
| 5 | Thực hành serverless automation và monitoring nâng cao. | 01/10/2026 | 01/10/2026 | [AWS Lambda](https://000022.awsstudygroup.com/)<br>[CloudWatch and Grafana](https://000029.awsstudygroup.com/)<br>[CloudWatch Advanced Workshop](https://000036.awsstudygroup.com/) |

### Kết quả đạt được

- Tìm hiểu CloudWatch Metrics, Logs, Alarms và Dashboard để theo dõi hoạt động tài nguyên và ứng dụng.
- Ôn tập DNS trên Route 53, gồm hosted zone, DNS record và cách DNS đưa người dùng đến endpoint ứng dụng.
- Hiểu AWS CLI là giao diện có thể script hóa để truy vấn, quản lý tài nguyên; lệnh sử dụng credentials và Region của profile đang cấu hình.
- Liên hệ quy trình vận hành: dùng CLI kiểm tra tài nguyên, CloudWatch quan sát hoạt động và DNS định tuyến người dùng đến endpoint.
- Ôn tập DynamoDB table, primary key, query pattern, chế độ read/write capacity và secondary index; cần thiết kế key dựa trên access pattern.
- Hiểu ElastiCache lưu dữ liệu trong bộ nhớ để giảm truy vấn lặp và độ trễ, đồng thời cần chiến lược invalidation cache phù hợp.
- Tìm hiểu CloudFront distribution, origin, cache behavior và edge delivery để giảm latency cũng như tải trực tiếp lên origin.
- Phân biệt vai trò của database lưu trữ bền vững, cache dữ liệu tạm và cache phân phối nội dung.
- Ôn tập cách các thành phần VPC và quy tắc định tuyến phối hợp để kết nối workload an toàn.
- Tìm hiểu CloudFront và Lambda@Edge: edge function có thể xử lý một số logic gần người dùng hơn tại các phase request/response được hỗ trợ.
- Biết cân nhắc đặt logic ở application origin hay edge dựa trên latency, xử lý request và giới hạn runtime hỗ trợ.
- Ghi nhớ một số workshop networking có thể tạo tài nguyên tính phí, cần theo dõi kiến trúc lab và dọn dẹp sau thực hành.
- Hiểu mô hình thực thi theo sự kiện của Lambda: function chạy khi được invoke hoặc nhận event mà không cần tự quản lý cụm máy chủ.
- Ôn tập cấu hình function, quyền truy cập, log, xử lý lỗi và tầm quan trọng của khả năng quan sát tiến trình xử lý event.
- Tìm hiểu dashboard CloudWatch/Grafana để hiển thị metric và theo dõi xu hướng.
- Nhận biết metrics, logs, alarms và dashboard trả lời các câu hỏi vận hành khác nhau, hiệu quả hơn khi được thiết kế theo tín hiệu có ý nghĩa.

### Bài học rút ra

- Metric và log cần có sẵn trước sự cố, không phải sau.
- Lambda bỏ việc quản lý máy chủ, nhưng timeout, bộ nhớ và quyền vẫn cần được thiết kế.

### Bước tiếp theo

Thực hành vận hành và hạ tầng dưới dạng mã ở Tuần 4.
