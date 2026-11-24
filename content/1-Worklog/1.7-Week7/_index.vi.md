---
title: "Worklog Tuần 7"
date: 2026-10-26
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

Trong Tuần 7 (26/10/2026–30/10/2026), tôi học CI/CD, phân tích chi phí và xây nền tảng serverless với Lambda, DynamoDB, API Gateway và AWS SAM.

### Mục tiêu tuần 7

- Tìm hiểu CI/CD với CodePipeline, các lựa chọn cam kết chi phí, phân tích chi phí với Glue và Athena.
- Xây dựng nền tảng serverless với Lambda, DynamoDB, Amplify, API Gateway và AWS SAM.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Tìm hiểu quy trình triển khai bằng CodePipeline và các lựa chọn tối ưu chi phí dài hạn. | 26/10/2026 | 26/10/2026 | [CodePipeline CI/CD](https://000017.awsstudygroup.com/)<br>[Automated CodePipeline Deployments](https://000023.awsstudygroup.com/)<br>[Savings Plans and Reserved Instances](https://000042.awsstudygroup.com/) |
| 3 | Tìm hiểu DevOps pipeline, trực quan hóa chi phí, Glue và Athena. | 27/10/2026 | 27/10/2026 | [ECS Applications CI/CD / DevOps with CodePipeline](https://000152.awsstudygroup.com/)<br>[Cost Visualization and Analytics](https://000034.awsstudygroup.com/)<br>[AWS Glue and Amazon Athena](https://000040.awsstudygroup.com/) |
| 4 | Xây dựng nền tảng CRUD serverless với Lambda và DynamoDB. | 28/10/2026 | 28/10/2026 | [Serverless CRUD with Lambda and DynamoDB](https://000133.awsstudygroup.com/) |
| 5 | Tích hợp xác thực/lưu trữ, API Gateway và AWS SAM. | 29/10/2026 | 29/10/2026 | [AWS Amplify](https://000134.awsstudygroup.com/)<br>[API Gateway Integration](https://000135.awsstudygroup.com/)<br>[AWS SAM Deployment](https://000136.awsstudygroup.com/) |

### Kết quả đạt được

- Hiểu mục đích của CI/CD pipeline: tự động hóa các giai đoạn như lấy source, build, test, approval và deploy tùy thiết kế pipeline.
- Phân biệt vai trò điều phối của pipeline với các thao tác build/deploy do provider được cấu hình thực hiện.
- Tìm hiểu Savings Plans và Reserved Instances là các lựa chọn giá dựa trên cam kết; cần đánh giá theo độ ổn định workload, mẫu sử dụng và điều kiện áp dụng.
- Nhận biết tự động hóa triển khai giúp tăng tính lặp lại nhưng vẫn cần giới hạn quyền, kế hoạch rollback và kiểm tra sau deploy.
- Ôn tập quy trình DevOps triển khai ứng dụng, bao gồm định nghĩa được quản lý phiên bản và khả năng truy vết thay đổi.
- Tìm hiểu cách trực quan hóa, so sánh chi phí cloud theo service và các chiều dữ liệu khác để nhận diện nhóm chi phí lớn.
- Hiểu vai trò AWS Glue trong catalog dữ liệu và ETL, còn Athena cho phép truy vấn SQL trên các nguồn dữ liệu được hỗ trợ như S3.
- Liên hệ dữ liệu chi phí/usage với analytics: kết luận đáng tin cậy phụ thuộc vào hiểu dataset, partitioning, phạm vi truy vấn và cả chi phí phân tích.
- Ôn tập kiến trúc workshop Document Management System và cách kết hợp Lambda với DynamoDB để xử lý metadata tài liệu và thao tác ứng dụng.
- Hiểu cách ánh xạ thao tác CRUD thành function handler, thao tác database, validation đầu vào, phân quyền và response lỗi.
- Luyện tư duy thiết kế key/access pattern của DynamoDB trước khi chọn query, đồng thời chỉ cấp cho Lambda execution role những quyền cần thiết.
- Nhận biết cần kiểm thử function, bảng dữ liệu và tài nguyên liên quan, đồng thời dọn dẹp khi kết thúc lab.
- Tìm hiểu Amplify hỗ trợ frontend tích hợp xác thực và lưu trữ; trong kiến trúc workshop, Cognito và S3 cung cấp các năng lực AWS nền tảng.
- Hiểu API Gateway là cổng API được quản lý, định tuyến request đến backend integration như Lambda.
- Tìm hiểu AWS SAM dùng để mô tả, đóng gói tài nguyên serverless và triển khai thông qua cơ chế quản lý hạ tầng dựa trên CloudFormation.
- Liên kết xác thực, công khai API, thực thi Lambda, lưu dữ liệu và triển khai hạ tầng thành một luồng request thống nhất.

### Bài học rút ra

- SAM giúp ứng dụng serverless có thể dựng lại từ một template duy nhất.
- Thiết kế key DynamoDB nên bắt đầu từ access pattern.

### Bước tiếp theo

Hoàn thiện bức tranh serverless và tổng kết kiến thức ở Tuần 8.
