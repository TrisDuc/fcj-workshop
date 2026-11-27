---
title: "Blog 2 - Test luồng Serverless & Event-driven (SQS + DLQ + DynamoDB) trên local với LocalStack và Java 21"
date: 2026-11-25
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

### Thông tin bài đăng

- **Tác giả:** Ngô Thị Kim Ngân (tên hiển thị Facebook: Ngô Thị Kim Ngân), thành viên trong nhóm.
- **Ngày đăng:** 25/11/2026, lúc 21:28 theo ảnh bài đăng.
- **Nơi đăng:** AWS Study Group VN trên Facebook.
- **Bài gốc:** TBD — dán link bài đăng Facebook vào đây.

### Test luồng Serverless & Event-driven không tốn tiền trên local: LocalStack + Java 21

**Bài toán:** Một luồng bất đồng bộ nhận sự kiện, đưa vào Amazon SQS, rồi được xử lý bởi AWS Lambda Java 21 để ghi DynamoDB và đọc Amazon SSM Parameter Store, kèm SQS dead-letter queue (DLQ) cho trường hợp lỗi. Đẩy thẳng lên AWS để debug từng lỗi nhỏ vừa chậm vừa dễ phát sinh chi phí ngoài ý muốn. Backend của nhóm cũng dùng cách này: Docker với LocalStack để chạy local, sau đó dùng AWS SAM để triển khai.

{{< event-photo src="images/blogs/localstack/architecture.png" alt="Developer và script test gửi payload vào hàng đợi SQS chính trong LocalStack; Lambda handler Java 21 ghi DynamoDB và đọc SSM; lỗi chuyển vào DLQ; sau khi kiểm tra local, cùng thiết kế được triển khai bằng AWS SAM lên ap-southeast-1." caption="Kiến trúc trong bài: test local bằng LocalStack, sau đó triển khai lên AWS bằng AWS SAM." >}}

#### Cách làm Local-First
Luồng được giả lập ngay trên máy bằng LocalStack: SQS, DLQ, DynamoDB và SSM. Cả luồng chạy đúng lẫn luồng lỗi đều được test trước khi build và triển khai bằng AWS SAM.

#### Ba điểm thực tế
- **Tách endpoint sạch trong Java 21 SDK v2.** Chỉ gọi `endpointOverride(URI.create(localEndpoint))` khi chạy chế độ `local` (`http://localhost:4566`). Lên cloud thì dùng endpoint mặc định. Trong sơ đồ đây là cờ `isLocal`: true ở local và false trên AWS.
- **Test cả happy path lẫn DLQ.** Payload hợp lệ phải được ghi vào DynamoDB và bị xóa khỏi queue chính. Payload gây lỗi phải rơi vào DLQ sau số lần retry đã cấu hình.
- **Lên cloud muộn hơn.** Chỉ deploy bằng AWS SAM khi logic và cấu hình đã ổn ở local, tránh vòng lặp sửa lỗi và deploy lại tốn kém trên môi trường thật.

#### Điểm rút ra
- DynamoDB trong luồng này còn lưu trạng thái và dữ liệu idempotency, nên một message xử lý hai lần không gây tác động trùng lặp.
- Test local chứng minh logic và hành vi retry; lần kiểm tra cuối trên AWS vẫn cần để xác nhận quyền và quota thật.

Bạn test luồng event-driven bằng LocalStack, SAM local hay test thẳng trên AWS?

### Minh chứng bài đã đăng

{{< event-photo src="images/blogs/localstack/facebook-publication.png" alt="Ảnh bài đăng của Ngô Thị Kim Ngân trong AWS Study Group VN, ngày 25 tháng 11 lúc 21:28." caption="Ảnh bài Blog 2 đã đăng trên AWS Study Group VN." >}}

### Tài liệu tham khảo

- [Tài liệu LocalStack](https://docs.localstack.cloud/)
- [AWS Serverless Application Model (AWS SAM)](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)
- [Amazon SQS dead-letter queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html)
