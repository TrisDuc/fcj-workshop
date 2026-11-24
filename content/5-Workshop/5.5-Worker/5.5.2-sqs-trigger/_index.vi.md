---
title : "SQS trigger, retry và idempotency"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.5.2 </b> "
---
#### 1. Thêm worker vào `template.yaml`
```yaml
  WorkerFunction:
    Type: AWS::Serverless::Function
    Properties:
      FunctionName: !Sub askfirst-${Stage}-worker
      CodeUri: worker/
      Handler: com.askfirst.worker.WorkerHandler::handleRequest
      MemorySize: 1024
      Timeout: 90
      Environment:
        Variables:
          AI_FUNCTION_NAME: !Sub askfirst-${Stage}-ai
          TOKEN_PARAM: /askfirst/dev/telegram/bot-token
      Events:
        Jobs:
          Type: SQS
          Properties:
            Queue: !GetAtt JobsQueue.Arn
            BatchSize: 1
            FunctionResponseTypes: [ReportBatchItemFailures]
      Policies:
        - DynamoDBCrudPolicy: { TableName: !Ref MainTable }
        - SQSSendMessagePolicy: { QueueName: !GetAtt JobsQueue.QueueName }
        - SSMParameterReadPolicy: { ParameterName: askfirst/dev/telegram/* }
        - LambdaInvokePolicy: { FunctionName: !Sub askfirst-${Stage}-ai }
```
+ `ReportBatchItemFailures` cho phép function báo đúng message nào lỗi để chỉ những message đó được thử lại.
+ Hãy triển khai function AI (mục 5.6) trước lần dùng đầu tiên; quyền invoke của worker tham chiếu tới nó theo tên.

#### 2. Các loại job
| `jobType` | Ý nghĩa |
| --- | --- |
| `ANALYZE` | Đọc buffer, gọi Lambda AI, trả lời |
| `ANSWER` | Gửi câu trả lời hoặc tin nhắn hướng dẫn đã chuẩn bị |
| `FOLLOWUP` | Hỏi "Bác đã chuyển tiền chưa?" (xếp hàng với độ trễ 15 phút) |

#### 3. Idempotency
SQS giao **at-least-once** nên một job có thể được giao hai lần. Trước khi gửi phản hồi, ghi một khóa bằng conditional write:
```java
PutItemRequest.builder().tableName(table)
  .item(Map.of("PK", s("USER#" + chatId), "SK", s("DONE#" + jobId), "ttl", n(now + 86_400)))
  .conditionExpression("attribute_not_exists(PK)")
  .build();
```
Nếu điều kiện thất bại, phản hồi đã được gửi: xác nhận job và dừng.

#### 4. Retry và DLQ
+ Exception được ném ra (hoặc một mục được báo lỗi) khiến message hiện lại sau visibility timeout.
+ Sau **5** lần nhận, SQS chuyển nó sang `askfirst-dev-jobs-dlq`.
+ Không nuốt lỗi âm thầm: để lỗi thật nổi lên để được retry hoặc đưa vào dead-letter.

#### 5. Build và triển khai
```
mvn -q -pl worker -am package
sam build && sam deploy
```
