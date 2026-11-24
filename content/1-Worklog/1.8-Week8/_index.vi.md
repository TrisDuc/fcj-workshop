---
title: "Worklog Tuần 8"
date: 2026-11-02
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

Trong Tuần 8 (02/11/2026–06/11/2026), tôi hoàn thiện bức tranh serverless với CDN, tìm kiếm và tracing, ôn ECS/Fargate và EKS, rồi tổng kết lộ trình học trước khi vào dự án.

### Mục tiêu tuần 8

- Hoàn thiện bức tranh ứng dụng serverless: CDN, tìm kiếm, DevOps serverless và distributed tracing.
- Ôn tập ECS/Fargate và EKS, sau đó tổng kết lộ trình học tám tuần và chuẩn bị cho dự án AskFirst (tuần 9–12).

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Bổ sung CDN, tìm kiếm và triển khai tự động cho ứng dụng serverless. | 02/11/2026 | 02/11/2026 | [CloudFront content delivery](https://000137.awsstudygroup.com/)<br>[Adding Search Functionality](https://000138.awsstudygroup.com/)<br>[Serverless DevOps](https://000139.awsstudygroup.com/) |
| 3 | Theo dõi request serverless và ôn tập CI/CD cho ECS/Fargate. | 03/11/2026 | 03/11/2026 | [X-Ray and CloudWatch tracing](https://000140.awsstudygroup.com/)<br>[ECS and AWS Fargate](https://000067.awsstudygroup.com/)<br>[CI/CD for ECS Applications](https://000152.awsstudygroup.com/) |
| 4 | Tìm hiểu EKS, EKS Blueprints for CDK, CI/CD cho Kubernetes và tổng kết tám tuần học. | 04/11/2026 | 04/11/2026 | [Amazon EKS](https://000126.awsstudygroup.com/)<br>[EKS Blueprints for CDK](https://000065.awsstudygroup.com/)<br>[CI/CD for EKS Applications](https://000062.awsstudygroup.com/) |

### Kết quả đạt được

- Ôn tập vai trò CloudFront, DNS và TLS khi phân phối ứng dụng web hiệu quả, an toàn; cấu hình certificate/domain phải phù hợp kiến trúc triển khai.
- Tìm hiểu phần mở rộng tìm kiếm cho ứng dụng quản lý tài liệu, bao gồm vai trò của search service và quy trình indexing theo cấu hình workshop.
- Hiểu CI/CD pipeline giúp đóng gói và triển khai thay đổi serverless nhất quán thay vì chỉ thao tác thủ công trên console.
- Xem xét vấn đề vận hành như độ mới của index, xử lý deploy lỗi, invalidation cache và cấu hình môi trường an toàn.
- Tìm hiểu distributed tracing theo dõi request qua các thành phần ứng dụng được hỗ trợ và trace map của X-Ray giúp nhận diện đoạn bị chậm hoặc lỗi.
- Liên hệ logs, metrics và traces: mỗi loại cung cấp bằng chứng khác nhau khi chẩn đoán sự cố production.
- Ôn tập ECS với Fargate cho phép chạy container mà không trực tiếp quản lý cụm EC2 nền tảng, nhưng vẫn cần định nghĩa task, networking, quyền và scaling.
- Tìm hiểu pipeline có thể build container image, đẩy lên registry và triển khai bản cập nhật ECS cùng bước kiểm tra và phương án rollback phù hợp.
- Ôn tập Amazon EKS là dịch vụ quản lý control plane Kubernetes và phân biệt khái niệm cluster, node, pod, deployment, service với các thành phần của ECS.
- Tìm hiểu EKS Blueprints for CDK như phương pháp/công cụ định nghĩa môi trường EKS và tài nguyên AWS liên quan theo cách có thể tái sử dụng.
- Ôn quy trình CI/CD cho Kubernetes ở mức tổng quan: build/test ứng dụng, đóng gói và publish image, cập nhật cấu hình deployment và kiểm tra rollout.
- Tổng hợp lộ trình học tám tuần (tuần 1–8) gồm dịch vụ AWS nền tảng, vận hành, bảo mật, độ tin cậy, quản lý chi phí, serverless, observability và container.
- Xác định nội dung cần thực hành tiếp: tự làm lại một số lab, ghi tài liệu kiến trúc, rà quyền IAM và dọn dẹp tài nguyên tính phí sau mỗi buổi.

### Bài học rút ra

- Distributed tracing là cần thiết khi một request đi qua nhiều dịch vụ.
- Chọn giữa Lambda và container phụ thuộc hình dạng workload và công sức vận hành.

### Bước tiếp theo

Bắt đầu dự án AskFirst ở Tuần 9: yêu cầu, kiến trúc và nền tảng.
