---
title: "Week 12 Worklog"
date: 2026-11-30
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

{{% notice info %}}
This week is the planned work for the AskFirst project. Update dates and results to match what was actually built and tested.
{{% /notice %}}

During Week 12 (30 November–4 December 2026), i hardened the system with IAM, secrets, keyless CI/CD, monitoring, and budgets, ran end-to-end tests, cleaned up resources, and finalized the report.

### Week 12 Objectives

- Harden the system: least-privilege IAM, secrets, encryption, keyless CI/CD, monitoring, alarms, and budgets.
- Run end-to-end tests, clean up resources, and finalize the proposal, workshop, and report.

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | IAM least-privilege roles per function; bot token in SSM Parameter Store; encryption with KMS. | 30 November 2026 | 30 November 2026 | [AWS IAM](https://000002.awsstudygroup.com/)<br>[AWS Secrets Manager](https://000096.awsstudygroup.com/) |
| Tuesday | Keyless deployment: GitHub Actions with OIDC to assume an IAM role, then `sam deploy`. | 1 December 2026 | 1 December 2026 | [AWS CloudFormation](https://000037.awsstudygroup.com/)<br>[CodePipeline CI/CD](https://000017.awsstudygroup.com/) |
| Wednesday | Observability and cost: CloudWatch logs and metrics, alarms to SNS email, CloudTrail, and AWS Budgets. | 2 December 2026 | 2 December 2026 | [Amazon CloudWatch](https://000008.awsstudygroup.com/)<br>[AWS Budgets](https://000007.awsstudygroup.com/) |
| Thursday | End-to-end testing: scam text, screenshot, link, oversized media, duplicate update, and DLQ replay. | 3 December 2026 | 3 December 2026 | Test plan / Kế hoạch kiểm thử |
| Friday | Clean up billable resources; finalize the proposal, workshop guide, blogs, events, and self-evaluation. | 4 December 2026 | 4 December 2026 | — |

### Achievements

- Gave each function its own role limited to the exact tables, queues, buckets, and APIs it needs.
- Moved the bot token to an encrypted SSM parameter and granted read access only to the functions that send messages.
- Reviewed which resources need customer-managed KMS keys versus AWS-managed encryption to balance security and cost.
- Registered GitHub as an OIDC identity provider and created a deployment role restricted to the project repository.
- Updated the workflow to deploy without storing long-lived AWS access keys.
- Confirmed that pull requests only build and test, while deployment runs from the main branch.
- Set alarms on DLQ depth, function errors, and AI Lambda duration, notifying the team by email through SNS.
- Enabled CloudTrail for auditing and set a monthly budget with alert thresholds.
- Set log retention periods to avoid paying for logs that are no longer useful.
- Ran each scenario through Telegram and compared results with expected risk levels.
- Measured end-to-end latency and per-check cost, and noted cold-start impact of the AI Lambda.
- Recorded defects and fixes, and confirmed that rejected or failed jobs behave as specified.
- Deleted the development stack, ECR images, S3 objects, and log groups that are no longer needed.
- Updated the documentation so every step can be repeated by another learner.
- Wrote a short retrospective: what worked, what was costly, and what to improve next.

### Lessons Learned

- Alarms and budgets should be created before the first deployment, not after.
- Measuring latency and cost per check shows where optimization matters.

### Next Steps

Review the full report and prepare for the final presentation.
