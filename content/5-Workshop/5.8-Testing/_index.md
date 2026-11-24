---
title : "End-to-end testing"
date : 2026-10-19
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---
Run each scenario through Telegram, then check Telegram, DynamoDB, and CloudWatch Logs.

| # | Scenario | Expected result |
| --- | --- | --- |
| 1 | `/start` | Immediate welcome and consent flow (fast path) |
| 2 | Scam-like text (urgent prize, "transfer a fee to receive") | High risk, plain-language reasons, advice not to transfer |
| 3 | Normal message from family ("dinner at 7?") | Low risk |
| 4 | Screenshot of a scam message | OCR text used; risk reflects the image content |
| 5 | Message containing a new or flagged link | Link evidence shown; second check hits the cache |
| 6 | Three short messages within 3 seconds | One grouped analysis and one reply |
| 7 | File over 20 MB, voice over 120 seconds | Friendly rejection; **nothing** queued |
| 8 | Same update sent twice | Second request acknowledged, no duplicate reply |
| 9 | High-risk result with family-alert consent | Family member receives a short summary only |
| 10 | 15 minutes after a risky verdict | Follow-up question arrives |
| 11 | Force the AI function to fail | Retries up to 5 times, then DLQ, alarm email |

#### Replay messages from the DLQ after a fix
```
DLQ_ARN=$(aws cloudformation describe-stacks --stack-name askfirst-dev \
  --query "Stacks[0].Outputs[?OutputKey=='JobsDlqArn'].OutputValue" --output text)
aws sqs start-message-move-task --source-arn $DLQ_ARN
aws sqs list-message-move-tasks --source-arn $DLQ_ARN
```

#### Measure
+ **Latency:** time from sending a message to receiving the verdict; note the first call after idle (cold start).
+ **Cost per check:** AI Lambda duration × memory, Bedrock tokens, and other charges, then compare with the proposal estimate.
+ **Quality:** keep a small table of test messages, expected risk, and actual risk; list any wrong verdicts and what you changed.

Record screenshots (with secrets hidden) for the report.
