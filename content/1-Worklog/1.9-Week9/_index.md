---
title: "Week 9 Worklog"
date: 2026-11-09
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

{{% notice info %}}
This week is the planned work for the AskFirst project. Update dates and results to match what was actually built and tested.
{{% /notice %}}

During Week 9 (9–13 November 2026), i started the AskFirst project: defined the problem and scope, wrote the requirements, designed the architecture, and prepared the repository and foundation.

### Week 9 Objectives

- Define the AskFirst problem, users, and scope: a Telegram assistant that helps older adults check suspicious messages before transferring money.
- Produce the requirements (SRS) and target architecture, and prepare the repository, CI, and development environment.

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | Review the research on online fraud in Vietnam; define personas (older adult, family member) and the Telegram-only scope. | 9 November 2026 | 9 November 2026 | Project docs / Tài liệu dự án |
| Tuesday | Write the SRS: FRS, NFRS, and Business Rules, starting with an initial-setup/consent feature group. | 10 November 2026 | 10 November 2026 | ScamProtection_System_Requirements.xlsx |
| Wednesday | Design the target architecture and choose Regions; draw the end-to-end flow. | 11 November 2026 | 11 November 2026 | [AWS Well-Architected](https://aws.amazon.com/architecture/well-architected/) |
| Thursday | Set up the repository, Maven multi-module layout, GitHub Actions CI, and the SAM development stack. | 12 November 2026 | 12 November 2026 | [AWS SAM](https://000136.awsstudygroup.com/) |
| Friday | Design the DynamoDB single-table model and create the SQS queue with a dead-letter queue. | 13 November 2026 | 13 November 2026 | [Amazon DynamoDB](https://000060.awsstudygroup.com/)<br>[SQS and SNS](https://000077.awsstudygroup.com/) |

### Achievements

- Summarized common scam patterns (impersonation, fake investment, malicious links) that the bot must recognize.
- Chose Telegram as the single channel so every requirement can be satisfied by what a Telegram bot can actually do.
- Listed the legal baseline to respect (Law 91/2025/QH15 and Decree 356/2025/NĐ-CP) and the need for an elder-friendly interface.
- Organized functional requirements into groups: initial setup and consent, message and media analysis, risk assessment, family alerts, bot guidance, and reporting.
- Separated non-functional requirements (latency, availability, privacy, cost) from business rules.
- Exported the three sheets (FRS, NFRS, Business Rules) to a single Excel file for review.
- Drew the serverless flow: Telegram → webhook Lambda → SQS → worker Lambda → AI Lambda → Telegram, with DynamoDB as the single data store.
- Placed the core stack in ap-southeast-1 (Singapore) for latency, and the Amazon Bedrock model and guardrails in ap-southeast-2 (Sydney).
- Decided that uploaded images stay only in the AI Lambda's memory and are not written to storage.
- Created modules `common`, `webhook-handler`, and `worker` (package `com.askfirst`) targeting Java 21.
- Added a GitHub Actions workflow that builds and tests on Java 21 for every push.
- Drafted `template.yaml` for the `askfirst-dev` stack in ap-southeast-1 and a Docker + LocalStack setup for local runs.
- Listed access patterns first (message buffer per user, session, check results, flags, family alerts) and derived partition/sort keys from them.
- Configured the job queue so a message is retried up to 5 times before moving to the DLQ.
- Confirmed the plan to store the Telegram bot token in SSM Parameter Store rather than in code or environment files.

### Lessons Learned

- Writing access patterns before the table design avoids rework in DynamoDB.
- Limiting the scope to one channel (Telegram) keeps every requirement achievable.

### Next Steps

Implement the webhook and worker Lambdas in Week 10.
