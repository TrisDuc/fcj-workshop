---
title: "Week 5 Worklog"
date: 2026-10-12
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

During Week 5 (12–16 October 2026), i strengthened governance and cost control and studied security building blocks such as private S3 access, WAF, Secrets Manager, and Cognito.

### Week 5 Objectives

- Strengthen governance and cost control: billing delegation, service quotas, cost and usage reporting.
- Study security building blocks: private S3 access, WAF, Secrets Manager, Cognito, S3 security practices.

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | Review billing delegation, service quotas, and cost and usage reporting. | 12 October 2026 | 12 October 2026 | [Billing Console Delegation](https://000075.awsstudygroup.com/)<br>[Service Quotas](https://000063.awsstudygroup.com/)<br>[Cost and Usage Management](https://000064.awsstudygroup.com/) |
| Tuesday | Study EBS lifecycle automation, backup anomaly detection, and developer tooling. | 13 October 2026 | 13 October 2026 | [EBS Data Lifecycle Manager](https://000088.awsstudygroup.com/)<br>[EBS Backup Anomaly Detection](https://000089.awsstudygroup.com/)<br>[AWS Toolkit for VS Code](https://000087.awsstudygroup.com/) |
| Wednesday | Study private S3 access, web application protection, and secrets management. | 14 October 2026 | 14 October 2026 | [S3 VPC Endpoints](https://000111.awsstudygroup.com/)<br>[AWS WAF](https://000026.awsstudygroup.com/)<br>[AWS Secrets Manager](https://000096.awsstudygroup.com/) |
| Thursday | Explore Cognito authentication and S3 security practices. | 15 October 2026 | 15 October 2026 | [Amazon Cognito](https://000141.awsstudygroup.com/)<br>[S3 Security Best Practices](https://000069.awsstudygroup.com/) |

### Achievements

- Learned why billing access should be delegated explicitly and separately from routine application permissions.
- Reviewed Service Quotas as account/Region limits that affect how much of a service can be provisioned, and where quota increase requests are managed.
- Explored cost and usage tools for reviewing spend by service, account, Region, or other available dimensions.
- Connected budgets, quotas, tagging, and usage reports as complementary controls for avoiding surprise costs and planning capacity.
- Reviewed EBS Data Lifecycle Manager policies for automating EBS snapshot and supported image lifecycle tasks according to defined schedules and retention rules.
- Learned the purpose of backup anomaly detection: identify unusual backup activity or patterns that may require investigation; alerts still need human review.
- Explored AWS Toolkit for VS Code as an integrated way to browse AWS resources and work with supported services from the editor.
- Recognized that backups need retention, restore testing, alerting, and cost review—not only successful snapshot creation.
- Learned how VPC endpoints can provide private connectivity to supported AWS services, with endpoint type and policy chosen for the intended access path.
- Reviewed AWS WAF as a layer for inspecting and filtering web requests using managed or custom rules; it complements rather than replaces secure application design.
- Learned to store and retrieve sensitive values such as database credentials through Secrets Manager instead of embedding them in source code or images.
- Recognized that private connectivity, request filtering, secret rotation, and least-privilege IAM are separate controls that work together.
- Reviewed Cognito user pools and identity concepts for application sign-up/sign-in and token-based authentication; authorization of application data remains an application design concern.
- Learned S3 security practices such as blocking unintended public access, using scoped bucket policies, enabling appropriate encryption, and auditing access.
- Distinguished user authentication from AWS IAM authorization for service-to-service access.
- Connected user identity, API permissions, bucket policy, and object-level data access as different decision points in a secure application.

### Lessons Learned

- Secrets belong in a managed secret store, never in code.
- Quotas and billing reports should be reviewed early to avoid surprises.

### Next Steps

Learn backup, messaging with SQS/SNS, and containers in Week 6.
