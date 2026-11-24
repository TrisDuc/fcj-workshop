---
title : "Monitoring, alarms, audit, and budgets"
date : 2026-10-19
weight : 3
chapter : false
pre : " <b> 5.7.3 </b> "
---
#### 1. Budget first
Console → **Billing and Cost Management → Budgets → Create budget**:
+ Type: **Cost budget**, monthly, amount of your choice (for example 10 USD).
+ Alerts: **80% actual** and **100% forecasted**, to your email.

#### 2. Notification topic (SNS)
```
TOPIC_ARN=$(aws sns create-topic --name askfirst-dev-alerts --query TopicArn --output text)
aws sns subscribe --topic-arn $TOPIC_ARN --protocol email \
  --notification-endpoint you@example.com
```
Open the confirmation email and confirm the subscription.

#### 3. Alarms
Dead-letter queue is not empty:
```
aws cloudwatch put-metric-alarm --alarm-name askfirst-dev-dlq-not-empty \
  --namespace AWS/SQS --metric-name ApproximateNumberOfMessagesVisible \
  --dimensions Name=QueueName,Value=askfirst-dev-jobs-dlq \
  --statistic Maximum --period 300 --evaluation-periods 1 --threshold 0 \
  --comparison-operator GreaterThanThreshold --treat-missing-data notBreaching \
  --alarm-actions $TOPIC_ARN
```
Function errors (repeat for `webhook`, `worker`, and `ai`):
```
aws cloudwatch put-metric-alarm --alarm-name askfirst-dev-worker-errors \
  --namespace AWS/Lambda --metric-name Errors \
  --dimensions Name=FunctionName,Value=askfirst-dev-worker \
  --statistic Sum --period 300 --evaluation-periods 1 --threshold 0 \
  --comparison-operator GreaterThanThreshold --treat-missing-data notBreaching \
  --alarm-actions $TOPIC_ARN
```
AI function running close to its 60-second limit:
```
aws cloudwatch put-metric-alarm --alarm-name askfirst-dev-ai-slow \
  --namespace AWS/Lambda --metric-name Duration \
  --dimensions Name=FunctionName,Value=askfirst-dev-ai \
  --statistic Maximum --period 300 --evaluation-periods 1 --threshold 50000 \
  --comparison-operator GreaterThanThreshold --treat-missing-data notBreaching \
  --alarm-actions $TOPIC_ARN
```

#### 4. Log retention
```
for f in webhook worker ai; do
  aws logs put-retention-policy --log-group-name /aws/lambda/askfirst-dev-$f --retention-in-days 14
done
```

#### 5. Audit with CloudTrail
Console → **CloudTrail → Create trail**, record **management events**, and deliver to a private S3 bucket. This provides the audit history (who changed what, including role assumptions from CI/CD).

#### 6. Verify
Send a deliberately failing job to the queue and confirm that, after 5 attempts, the DLQ alarm sends an email.
