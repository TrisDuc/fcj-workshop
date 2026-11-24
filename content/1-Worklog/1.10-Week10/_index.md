---
title: "Week 10 Worklog"
date: 2026-11-16
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

{{% notice info %}}
This week is the planned work for the AskFirst project. Update dates and results to match what was actually built and tested.
{{% /notice %}}

During Week 10 (16–20 November 2026), i implemented the Java 21 webhook and worker Lambdas: authentication, de-duplication, media limits, buffering, job handling, and retries.

### Week 10 Objectives

- Implement the Java 21 webhook Lambda: authenticate Telegram, de-duplicate updates, reject oversized media, and enqueue jobs.
- Implement the Java 21 worker Lambda: aggregate messages per user, call the AI Lambda, and handle retries.

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | Webhook handler: constant-time secret check, de-duplication by `update_id`, fast path vs. slow path. | 16 November 2026 | 16 November 2026 | [Telegram Bot API](https://core.telegram.org/bots/api) |
| Tuesday | Media guard: reject images/files over 20 MB and voice over 120 seconds before any job is created. | 17 November 2026 | 17 November 2026 | Project issue tracker / Issue của dự án |
| Wednesday | Message buffering: BUFFER# META item plus MSG# items ordered by sequence, with a 3-second aggregation window per user. | 18 November 2026 | 18 November 2026 | [Amazon DynamoDB](https://000060.awsstudygroup.com/) |
| Thursday | Worker Lambda: SQS polling, job types (analyze, answer, follow-up), repositories, and the AI client contract. | 19 November 2026 | 19 November 2026 | [SQS and SNS](https://000077.awsstudygroup.com/) |
| Friday | Retries, dead-letter handling, idempotency, tests, and the first deployment to the dev stack. | 20 November 2026 | 20 November 2026 | [AWS SAM](https://000136.awsstudygroup.com/) |

### Achievements

- Compared the secret-token header with a constant-time comparison so timing differences reveal nothing.
- Used a conditional write in DynamoDB keyed by `update_id` so a repeated delivery is acknowledged but not processed twice.
- Split handling: simple commands answered immediately (fast path); message analysis queued (slow path) so the webhook returns quickly.
- Read size and duration from the Telegram update metadata and rejected oversized items with a clear message to the user.
- Ensured rejected items never reach SQS, so no worker or AI cost is incurred.
- Added unit tests for boundary values (exactly at and just over each limit).
- Stored each incoming message under the user's buffer with an increasing sequence number.
- Designed the worker to wait for the window to close, then read all messages in order and send them as one analysis job.
- Documented how late-arriving messages start a new window instead of being lost.
- Implemented the SQS event handler and dispatch by job type.
- Built an `AiClient` that invokes the Python AI Lambda using the agreed JSON contract, without changing the contract or the AI code.
- Added repositories for buffer, flags, checks, sessions, and alerts on top of the single table.
- Verified that a poison message is retried up to 5 times and then lands in the DLQ.
- Made job processing idempotent so a retry cannot send duplicate replies to the user.
- Deployed the webhook and worker to the `askfirst-dev` stack with `sam build` and `sam deploy`.

### Lessons Learned

- Delivery is at-least-once, so every handler must be idempotent.
- Rejecting bad input at the edge saves cost downstream.

### Next Steps

Build and connect the AI Lambda in Week 11.
