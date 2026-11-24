---
title : "Single-table DynamoDB"
date : 2026-10-19
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---
#### 1. Start from access patterns
Write the questions the system must answer, then derive keys from them.

| Access pattern | PK | SK |
| --- | --- | --- |
| Was this Telegram update already processed? | `UPD#<update_id>` | `DEDUP` |
| Buffer state of a user's pending messages | `BUFFER#<chat_id>` | `META` |
| Pending messages in order | `BUFFER#<chat_id>` | `MSG#<seq>` |
| User profile, consent, and feature flags | `USER#<chat_id>` | `PROFILE` |
| History of checks for a user | `USER#<chat_id>` | `CHECK#<timestamp>#<id>` |
| Cached result for a URL/domain | `URL#<sha256>` | `CACHE` |
| Family alert records | `USER#<chat_id>` | `ALERT#<timestamp>` |

Short-lived items (`UPD#`, `BUFFER#`, `URL#`) carry a numeric `ttl` attribute so DynamoDB deletes them automatically.

#### 2. Add the table to `template.yaml`
```yaml
Resources:
  MainTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: !Sub askfirst-${Stage}-main
      BillingMode: PAY_PER_REQUEST
      AttributeDefinitions:
        - { AttributeName: PK, AttributeType: S }
        - { AttributeName: SK, AttributeType: S }
      KeySchema:
        - { AttributeName: PK, KeyType: HASH }
        - { AttributeName: SK, KeyType: RANGE }
      TimeToLiveSpecification:
        AttributeName: ttl
        Enabled: true
      SSESpecification:
        SSEEnabled: true
```
+ **On-demand billing** suits unpredictable pilot traffic.
+ **TTL** removes buffers, de-duplication markers, and cache entries without extra code.

#### 3. Verify after deploy (section 5.3.2 deploys the stack)
```
aws dynamodb describe-table --table-name askfirst-dev-main \
  --query "Table.{Status:TableStatus,Billing:BillingModeSummary.BillingMode}"
```
Expected: `ACTIVE` and `PAY_PER_REQUEST`.
