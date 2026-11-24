---
title : "Foundation: DynamoDB and SQS"
date : 2026-10-19
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---
In this part you create the two resources every later step depends on: the **single DynamoDB table** and the **job queue with its dead-letter queue**. You will define them in `template.yaml` so they can be rebuilt or deleted with one command.

#### Content
- [Single-table DynamoDB](5.3.1-dynamodb/)
- [SQS queue and dead-letter queue](5.3.2-sqs-dlq/)
