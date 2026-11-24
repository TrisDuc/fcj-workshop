---
title : "SQS queue and dead-letter queue"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---
#### 1. Add the queues
```yaml
  JobsDlq:
    Type: AWS::SQS::Queue
    Properties:
      QueueName: !Sub askfirst-${Stage}-jobs-dlq
      MessageRetentionPeriod: 1209600      # 14 days to investigate
      SqsManagedSseEnabled: true

  JobsQueue:
    Type: AWS::SQS::Queue
    Properties:
      QueueName: !Sub askfirst-${Stage}-jobs
      VisibilityTimeout: 540               # see note below
      SqsManagedSseEnabled: true
      RedrivePolicy:
        deadLetterTargetArn: !GetAtt JobsDlq.Arn
        maxReceiveCount: 5                 # 5 attempts, then DLQ

Outputs:
  JobsQueueUrl:
    Value: !Ref JobsQueue
  JobsDlqArn:
    Value: !GetAtt JobsDlq.Arn
```
{{% notice note %}}
**Visibility timeout:** set it well above the consumer's timeout. The worker will have a 90-second timeout, so 540 seconds (six times) leaves room for retries without redelivering a message that is still being processed.
{{% /notice %}}

#### 2. Create the local file structure and deploy the foundation
Put the Globals block from below at the top of `template.yaml`, followed by the table and queues.
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
Accept the defaults and let SAM save them in `samconfig.toml`.

#### 3. Verify the retry path
```
QUEUE_URL=$(aws cloudformation describe-stacks --stack-name askfirst-dev \
  --query "Stacks[0].Outputs[?OutputKey=='JobsQueueUrl'].OutputValue" --output text)
aws sqs send-message --queue-url "$QUEUE_URL" --message-body '{"jobType":"PING"}'
aws sqs get-queue-attributes --queue-url "$QUEUE_URL" \
  --attribute-names RedrivePolicy
```
The `RedrivePolicy` must show `maxReceiveCount` 5 and the DLQ ARN. You will see messages move to the DLQ in the testing step.
