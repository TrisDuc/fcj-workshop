---
title : "Triển khai function webhook"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---
#### 1. Handler làm gì (theo thứ tự)
1. **Xác thực:** so sánh header secret bằng phép so sánh thời gian hằng.
2. **Chống trùng:** ghi `UPD#<update_id>` với điều kiện chưa tồn tại; nếu đã có thì trả `200` mà không xử lý.
3. **Chặn media:** từ chối ảnh/file trên **20 MB** và voice trên **120 giây** *trước khi* tạo bất kỳ job nào.
4. **Đường nhanh:** lệnh đơn giản (như `/start`) được trả lời ngay.
5. **Đường chậm:** các tin nhắn khác được gom vào buffer và đưa job vào hàng đợi.
6. **Luôn phản hồi nhanh** với HTTP `200` cho update hợp lệ để Telegram không retry không cần thiết.

#### 2. Các ý chính trong mã (Java 21)
Kiểm tra secret thời gian hằng:
```java
boolean valid = MessageDigest.isEqual(
    provided.getBytes(StandardCharsets.UTF_8),
    expected.getBytes(StandardCharsets.UTF_8));
```
Chống trùng bằng conditional write:
```java
PutItemRequest req = PutItemRequest.builder()
    .tableName(table)
    .item(Map.of(
        "PK",  AttributeValue.fromS("UPD#" + updateId),
        "SK",  AttributeValue.fromS("DEDUP"),
        "ttl", AttributeValue.fromN(String.valueOf(nowEpoch + 86_400))))
    .conditionExpression("attribute_not_exists(PK)")
    .build();
try { ddb.putItem(req); }
catch (ConditionalCheckFailedException dup) { return ok(); } // đã xử lý rồi
```
Chặn media (giới hạn lấy từ cấu hình):
```java
if (fileSize > MAX_FILE_BYTES || voiceSeconds > MAX_VOICE_SECONDS) {
    reply("Tệp này quá lớn. Bác vui lòng gửi tệp nhỏ hơn.");
    return ok();   // không đưa gì vào hàng đợi
}
```

#### 3. Thêm function vào `template.yaml`
```yaml
  WebhookFunction:
    Type: AWS::Serverless::Function
    Properties:
      FunctionName: !Sub askfirst-${Stage}-webhook
      CodeUri: webhook-handler/
      Handler: com.askfirst.webhook.WebhookHandler::handleRequest
      MemorySize: 512
      Timeout: 10
      AutoPublishAlias: live
      SnapStart:
        ApplyOn: PublishedVersions
      FunctionUrlConfig:
        AuthType: NONE            # URL công khai; được bảo vệ bằng header secret
      Environment:
        Variables:
          SECRET_PARAM: /askfirst/dev/telegram/webhook-secret
          TOKEN_PARAM: /askfirst/dev/telegram/bot-token
          MAX_FILE_BYTES: "20971520"
          MAX_VOICE_SECONDS: "120"
      Policies:
        - DynamoDBCrudPolicy: { TableName: !Ref MainTable }
        - SQSSendMessagePolicy: { QueueName: !GetAtt JobsQueue.QueueName }
        - SSMParameterReadPolicy: { ParameterName: askfirst/dev/telegram/* }

Outputs:
  WebhookUrl:
    Value: !GetAtt WebhookFunctionUrl.FunctionUrl
```
{{% notice note %}}
**SnapStart** giảm cold start của Java, điều này quan trọng vì Telegram mong đợi phản hồi nhanh. Khi đặt `AutoPublishAlias`, SAM tạo function URL trên alias.
{{% /notice %}}

#### 4. Build và triển khai
```
mvn -q -pl webhook-handler -am package
sam build
sam deploy
```
Sao chép output `WebhookUrl` cho bước tiếp theo.
