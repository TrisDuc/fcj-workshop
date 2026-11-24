---
title : "Worker Lambda"
date : 2026-10-19
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---
The worker consumes jobs from SQS. It turns a burst of short messages into **one** analysis request, calls the AI Lambda, sends the reply, and handles follow-ups.

#### Content
- [Message buffering with a 3-second window](5.5.1-buffering/)
- [SQS trigger, retries, and idempotency](5.5.2-sqs-trigger/)
- [Results, family alerts, and the 15-minute follow-up](5.5.3-results-followup/)
