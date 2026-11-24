---
title : "Giám sát, alarm, kiểm toán và ngân sách"
date : 2026-10-19
weight : 3
chapter : false
pre : " <b> 5.7.3 </b> "
---
#### 1. Ngân sách trước tiên
Console → **Billing and Cost Management → Budgets → Create budget**:
+ Loại: **Cost budget**, hằng tháng, số tiền tùy bạn (ví dụ 10 USD).
+ Cảnh báo: **80% thực tế** và **100% dự báo**, gửi tới email của bạn.

#### 2. Topic thông báo (SNS)
```
TOPIC_ARN=$(aws sns create-topic --name askfirst-dev-alerts --query TopicArn --output text)
aws sns subscribe --topic-arn $TOPIC_ARN --protocol email \
  --notification-endpoint you@example.com
```
Mở email xác nhận và xác nhận đăng ký.

#### 3. Alarm
Dead-letter queue không rỗng:
```
aws cloudwatch put-metric-alarm --alarm-name askfirst-dev-dlq-not-empty \
  --namespace AWS/SQS --metric-name ApproximateNumberOfMessagesVisible \
  --dimensions Name=QueueName,Value=askfirst-dev-jobs-dlq \
  --statistic Maximum --period 300 --evaluation-periods 1 --threshold 0 \
  --comparison-operator GreaterThanThreshold --treat-missing-data notBreaching \
  --alarm-actions $TOPIC_ARN
```
Lỗi function (lặp lại cho `webhook`, `worker` và `ai`):
```
aws cloudwatch put-metric-alarm --alarm-name askfirst-dev-worker-errors \
  --namespace AWS/Lambda --metric-name Errors \
  --dimensions Name=FunctionName,Value=askfirst-dev-worker \
  --statistic Sum --period 300 --evaluation-periods 1 --threshold 0 \
  --comparison-operator GreaterThanThreshold --treat-missing-data notBreaching \
  --alarm-actions $TOPIC_ARN
```
Function AI chạy gần giới hạn 60 giây:
```
aws cloudwatch put-metric-alarm --alarm-name askfirst-dev-ai-slow \
  --namespace AWS/Lambda --metric-name Duration \
  --dimensions Name=FunctionName,Value=askfirst-dev-ai \
  --statistic Maximum --period 300 --evaluation-periods 1 --threshold 50000 \
  --comparison-operator GreaterThanThreshold --treat-missing-data notBreaching \
  --alarm-actions $TOPIC_ARN
```

#### 4. Thời gian lưu log
```
for f in webhook worker ai; do
  aws logs put-retention-policy --log-group-name /aws/lambda/askfirst-dev-$f --retention-in-days 14
done
```

#### 5. Kiểm toán với CloudTrail
Console → **CloudTrail → Create trail**, ghi **management events** và chuyển vào một bucket S3 riêng tư. Đây là lịch sử kiểm toán (ai đã thay đổi gì, gồm cả việc assume role từ CI/CD).

#### 6. Kiểm tra
Gửi cố ý một job lỗi vào hàng đợi và xác nhận rằng sau 5 lần thử, alarm DLQ gửi email.
