---
title : "Kết quả, cảnh báo người thân và hỏi lại sau 15 phút"
date : 2026-10-19
weight : 3
chapter : false
pre : " <b> 5.5.3 </b> "
---
#### 1. Gửi kết luận
Lambda AI trả về kết quả có cấu trúc (mức rủi ro, lý do, lời khuyên ngắn). Worker:
1. Định dạng bằng **tiếng Việt đơn giản, bình tĩnh** kèm bước tiếp theo rõ ràng ("Chưa chuyển tiền; hãy gọi cho người thân hoặc ngân hàng theo số trên thẻ của bác").
2. Gửi bằng phương thức `sendMessage` của Telegram với token lấy từ SSM.
3. Lưu item `CHECK#<timestamp>#<id>` dưới người dùng.

#### 2. Cảnh báo người thân (chỉ khi có đồng ý)
```java
if (result.risk() == Risk.HIGH && profile.familyAlertConsent()) {
    for (FamilyContact c : profile.familyContacts()) {
        telegram.sendMessage(c.chatId(), alertText(profile.displayName(), result.summary()));
        alertRepo.record(profile.chatId(), c.chatId(), now);
    }
}
```
+ Chỉ chia sẻ **bản tóm tắt ngắn**, không bao giờ gửi nội dung tin nhắn gốc hoặc dữ liệu cá nhân đã che.
+ Ghi mỗi cảnh báo vào `ALERT#<timestamp>` để truy vết.

#### 3. Đặt lịch hỏi lại (bước 17)
```java
sqs.sendMessage(b -> b.queueUrl(queueUrl)
    .delaySeconds(900)                       // 15 phút, mức tối đa của SQS
    .messageBody(json(Map.of(
        "jobType", "FOLLOWUP",
        "chatId", chatId,
        "jobId", "FU#" + checkId))));
```
+ Nếu người dùng trả lời trước khi câu hỏi lại đến, worker có thể bỏ qua việc gửi bằng cách kiểm tra trạng thái của lần kiểm tra trước.
+ Với độ trễ dài hơn 15 phút, dùng Amazon EventBridge Scheduler (xem tài liệu Amazon EventBridge Scheduler).

#### 4. Kiểm tra
Gửi một tin nhắn thử; trong vài giây bạn sẽ nhận được phản hồi. Trong DynamoDB, item `CHECK#...` xuất hiện cho chat của bạn. Mười lăm phút sau, câu hỏi hỏi lại sẽ đến.
