---
title : "Deploy the webhook function"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---
#### 1. What the handler does (in order)
1. **Authenticate:** compare the secret header using a constant-time comparison.
2. **De-duplicate:** write `UPD#<update_id>` with a condition that it does not exist; if it does, return `200` without processing.
3. **Guard media:** reject images/files over **20 MB** and voice messages over **120 seconds** *before* any job is created.
4. **Fast path:** simple commands (such as `/start`) are answered immediately.
5. **Slow path:** other messages are buffered and a job is queued.
6. **Always return quickly** with HTTP `200` for valid updates so Telegram does not retry needlessly.

#### 2. Key code ideas (Java 21)
Constant-time secret check:
```java
boolean valid = MessageDigest.isEqual(
    provided.getBytes(StandardCharsets.UTF_8),
    expected.getBytes(StandardCharsets.UTF_8));
```
De-duplication with a conditional write:
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
catch (ConditionalCheckFailedException dup) { return ok(); } // already handled
```
Media guard (limits from configuration):
```java
if (fileSize > MAX_FILE_BYTES || voiceSeconds > MAX_VOICE_SECONDS) {
    reply("This file is too large. Please send a smaller one.");
    return ok();   // nothing is queued
}
```

#### 3. Add the function to `template.yaml`
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
        AuthType: NONE            # public URL; protected by the secret header
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
**SnapStart** reduces Java cold starts, which matters because Telegram expects a quick reply. When `AutoPublishAlias` is set, SAM creates the function URL on the alias.
{{% /notice %}}

#### 4. Build and deploy
```
mvn -q -pl webhook-handler -am package
sam build
sam deploy
```
Copy the `WebhookUrl` output for the next step.
