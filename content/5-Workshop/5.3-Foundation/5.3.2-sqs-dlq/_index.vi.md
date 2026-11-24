---
title : "Hàng đợi SQS và dead-letter queue"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---
#### 1. Thêm các hàng đợi
```yaml
  JobsDlq:
    Type: AWS::SQS::Queue
    Properties:
      QueueName: !Sub askfirst-${Stage}-jobs-dlq
      MessageRetentionPeriod: 1209600      # 14 ngày để điều tra
      SqsManagedSseEnabled: true

  JobsQueue:
    Type: AWS::SQS::Queue
    Properties:
      QueueName: !Sub askfirst-${Stage}-jobs
      VisibilityTimeout: 540               # xem ghi chú bên dưới
      SqsManagedSseEnabled: true
      RedrivePolicy:
        deadLetterTargetArn: !GetAtt JobsDlq.Arn
        maxReceiveCount: 5                 # 5 lần thử rồi vào DLQ

Outputs:
  JobsQueueUrl:
    Value: !Ref JobsQueue
  JobsDlqArn:
    Value: !GetAtt JobsDlq.Arn
```
{{% notice note %}}
**Visibility timeout:** đặt cao hơn nhiều so với timeout của consumer. Worker sẽ có timeout 90 giây, nên 540 giây (gấp sáu lần) chừa chỗ cho retry mà không giao lại message vẫn đang được xử lý.
{{% /notice %}}

#### 2. Tạo cấu trúc file local và triển khai phần nền tảng
Đặt khối Globals dưới đây ở đầu `template.yaml`, tiếp theo là bảng và các hàng đợi.
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Description: AskFirst dev stack

Parameters:
  Stage:
    Type: String
    Default: dev

Globals:
  Function:
    Runtime: java21
    Architectures: [arm64]
    Environment:
      Variables:
        STAGE: !Ref Stage
        TABLE_NAME: !Ref MainTable
        QUEUE_URL: !Ref JobsQueue
```
```
sam validate --lint
sam build
sam deploy --guided --stack-name askfirst-dev --region ap-southeast-1 \
  --capabilities CAPABILITY_IAM --parameter-overrides Stage=dev
```
Chấp nhận giá trị mặc định và để SAM lưu vào `samconfig.toml`.

#### 3. Kiểm tra đường retry
```
QUEUE_URL=$(aws cloudformation describe-stacks --stack-name askfirst-dev \
  --query "Stacks[0].Outputs[?OutputKey=='JobsQueueUrl'].OutputValue" --output text)
aws sqs send-message --queue-url "$QUEUE_URL" --message-body '{"jobType":"PING"}'
aws sqs get-queue-attributes --queue-url "$QUEUE_URL" \
  --attribute-names RedrivePolicy
```
`RedrivePolicy` phải hiển thị `maxReceiveCount` bằng 5 và ARN của DLQ. Bạn sẽ thấy message chuyển vào DLQ ở bước kiểm thử.
