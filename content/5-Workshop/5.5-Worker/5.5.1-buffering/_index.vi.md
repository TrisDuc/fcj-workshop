---
title : "Gom tin nhắn với cửa sổ 3 giây"
date : 2026-10-19
weight : 1
chapter : false
pre : " <b> 5.5.1 </b> "
---
Người dùng thường gửi nhiều tin ngắn liên tiếp (một câu, rồi một link, rồi một ảnh chụp màn hình). Phân tích riêng từng tin tốn tiền và mất ngữ cảnh.

#### Thiết kế
1. Webhook thêm từng tin nhắn vào buffer của người dùng: `BUFFER#<chat_id>` / `MSG#<seq>`.
2. `seq` lấy từ bộ đếm nguyên tử trên `BUFFER#<chat_id>` / `META`.
3. Chỉ tin nhắn **đầu tiên** của một cửa sổ mới đưa `AnalyzeJob` vào hàng đợi, với **độ trễ gửi 3 giây**. Các tin sau trong cùng cửa sổ chỉ thêm item `MSG#`.
4. Khi worker nhận job, nó đọc tất cả item `MSG#` theo thứ tự, xóa buffer và gửi **một** yêu cầu tới Lambda AI.

#### Phía webhook (phác thảo)
```java
// 1) tăng bộ đếm và biết cửa sổ đã mở hay chưa
UpdateItemResponse r = ddb.updateItem(UpdateItemRequest.builder()
    .tableName(table)
    .key(Map.of("PK", s("BUFFER#" + chatId), "SK", s("META")))
    .updateExpression("ADD seq :one SET ttl = :ttl, windowOpen = if_not_exists(windowOpen, :now)")
    .expressionAttributeValues(Map.of(":one", n(1), ":ttl", n(now + 600), ":now", n(now)))
    .returnValues(ReturnValue.ALL_NEW)
    .build());
long seq = Long.parseLong(r.attributes().get("seq").n());

// 2) lưu tin nhắn
ddb.putItem(msgItem(chatId, seq, text, fileRef));

// 3) tin đầu tiên của cửa sổ? -> đưa job vào hàng đợi với độ trễ 3 giây
if (seq == 1) {
    sqs.sendMessage(b -> b.queueUrl(queueUrl)
        .delaySeconds(3)
        .messageBody(json(Map.of("jobType", "ANALYZE", "chatId", chatId))));
}
```
{{% notice note %}}
Độ trễ gửi của SQS cho phép 0–900 giây nên 3 giây là hợp lệ. Tin nhắn đến sau khi worker đã xóa buffer sẽ mở một cửa sổ **mới** (`seq` bắt đầu lại từ 1) nên không có tin nào bị mất.
{{% /notice %}}

#### Phía worker (phác thảo)
```java
List<Msg> msgs = bufferRepo.readAllInOrder(chatId);   // Query PK=BUFFER#id, SK begins_with MSG#
bufferRepo.deleteAll(chatId);                         // sau khi đọc thành công
String grouped = Msg.join(msgs);
```
Chỉ xóa buffer sau khi đã có dữ liệu. Nếu worker lỗi trước khi gọi AI, SQS giao lại job và khóa idempotency (trang sau) ngăn gửi trùng phản hồi.
