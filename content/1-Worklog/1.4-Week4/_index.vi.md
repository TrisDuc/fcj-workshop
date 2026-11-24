---
title: "Worklog Tuần 4"
date: 2026-10-05
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

Trong Tuần 4 (05/10/2026–09/10/2026), tôi thực hành quản trị vận hành và học hạ tầng dưới dạng mã với CloudFormation và CDK.

### Mục tiêu tuần 4

- Thực hành quản trị vận hành: tag, Systems Manager, Session Manager.
- Tìm hiểu hạ tầng dưới dạng mã (CloudFormation, CDK), right-sizing và khả năng quan sát lưu lượng mạng.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Quản lý tag tài nguyên, phân quyền theo tag và Systems Manager. | 05/10/2026 | 05/10/2026 | [Tags and Resource Groups](https://000027.awsstudygroup.com/)<br>[IAM and Resource Tags](https://000028.awsstudygroup.com/)<br>[AWS Systems Manager](https://000031.awsstudygroup.com/) |
| 3 | Tìm hiểu Session Manager, CloudFormation và CDK. | 06/10/2026 | 06/10/2026 | [Session Manager](https://000058.awsstudygroup.com/)<br>[AWS CloudFormation](https://000037.awsstudygroup.com/)<br>[AWS CDK Advanced](https://000076.awsstudygroup.com/) |
| 4 | Kết hợp IaC, tối ưu cấu hình EC2 và VPC Flow Logs. | 07/10/2026 | 07/10/2026 | [Infrastructure as Code Workshop Series](https://000102.awsstudygroup.com/)<br>[EC2 Resource Optimization](https://000032.awsstudygroup.com/)<br>[VPC Flow Logs](https://000074.awsstudygroup.com/) |

### Kết quả đạt được

- Tìm hiểu cách chuẩn hóa tag để xác định owner, môi trường, ứng dụng, phân bổ chi phí và nhóm tài nguyên liên quan.
- Ôn tập kiểm soát truy cập theo tag và các giới hạn: policy cần thiết kế cẩn thận vì không phải action nào cũng áp dụng resource tag giống nhau.
- Khám phá Systems Manager với các khả năng như Run Command và Patch Manager để vận hành, bảo trì managed instance.
- Hiểu yêu cầu về IAM permission, instance role và kết nối của managed node để Systems Manager hoạt động.
- Tìm hiểu Session Manager là cách truy cập instance được hỗ trợ một cách có kiểm soát mà không cần mở cổng SSH/RDP inbound ra Internet, nếu cấu hình đủ điều kiện.
- Hiểu CloudFormation mô tả hạ tầng bằng template và quản lý tài nguyên triển khai theo stack, giúp lặp lại và theo dõi thay đổi.
- So sánh template khai báo với AWS CDK, cho phép định nghĩa hạ tầng bằng ngôn ngữ lập trình quen thuộc rồi tổng hợp thành CloudFormation.
- Nhận biết infrastructure code cần được review, tham số hóa, giới hạn quyền và dọn dẹp có chủ đích.
- Tổng hợp quy trình IaC: mô tả hạ tầng bằng file được quản lý phiên bản, review thay đổi, triển khai lặp lại và phát hiện drift ở nơi được hỗ trợ.
- Hiểu tối ưu EC2 cần dựa trên CPU, bộ nhớ, network và đặc trưng workload thực tế thay vì mặc định chọn instance lớn nhất.
- Ôn tập VPC Flow Logs là bản ghi metadata lưu lượng mạng để điều tra kết nối và mẫu traffic; không ghi lại payload của gói tin.
- Tổng kết chủ đề vận hành tuần này: quan sát hoạt động, quản lý tài nguyên nhất quán và tối ưu dựa trên dữ liệu.

### Bài học rút ra

- Tag và template giúp môi trường lặp lại được và dễ dọn dẹp.
- Khả năng quan sát lưu lượng mạng giúp xử lý sự cố nhanh hơn.

### Bước tiếp theo

Tìm hiểu quản trị, kiểm soát chi phí và các thành phần bảo mật ở Tuần 5.
