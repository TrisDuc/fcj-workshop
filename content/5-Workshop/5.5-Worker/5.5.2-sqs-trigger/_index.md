---
title : "SQS trigger, retries, and idempotency"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.5.2 </b> "
---
#### 1. Add the worker to `template.yaml`
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
+ `ReportBatchItemFailures` lets the function report exactly which messages failed, so only those are retried.
+ Deploy the AI function (section 5.6) before first use; the worker's invoke permission refers to it by name.

#### 2. Job types
| `jobType` | Meaning |
| --- | --- |
| `ANALYZE` | Read the buffer, call the AI Lambda, reply |
| `ANSWER` | Send a prepared answer or guidance message |
| `FOLLOWUP` | Ask "Did you go ahead with the transfer?" (queued with a 15-minute delay) |

#### 3. Idempotency
SQS is **at-least-once**, so a job may be delivered twice. Before sending a reply, record a key with a conditional write:
```java
PutItemRequest.builder().tableName(table)
  .item(Map.of("PK", s("USER#" + chatId), "SK", s("DONE#" + jobId), "ttl", n(now + 86_400)))
  .conditionExpression("attribute_not_exists(PK)")
  .build();
```
If the condition fails, the reply was already sent: acknowledge the job and stop.

#### 4. Retries and the DLQ
+ A thrown exception (or an item reported as failed) makes the message visible again after the visibility timeout.
+ After **5** receives, SQS moves it to `askfirst-dev-jobs-dlq`.
+ Never swallow errors silently: let a real failure surface so it is retried or dead-lettered.

#### 5. Build and deploy
```
mvn -q -pl worker -am package
sam build && sam deploy
```
