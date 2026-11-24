---
title: "Week 4 Worklog"
date: 2026-10-05
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

During Week 4 (5–9 October 2026), i practiced operational management and learned infrastructure as code with CloudFormation and CDK.

### Week 4 Objectives

- Practice operational management: tagging, Systems Manager, Session Manager.
- Learn infrastructure as code (CloudFormation, CDK), right-sizing, and network traffic visibility.

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | Manage resource tags, tag-based access, and Systems Manager. | 5 October 2026 | 5 October 2026 | [Tags and Resource Groups](https://000027.awsstudygroup.com/)<br>[IAM and Resource Tags](https://000028.awsstudygroup.com/)<br>[AWS Systems Manager](https://000031.awsstudygroup.com/) |
| Tuesday | Learn Session Manager, CloudFormation, and CDK. | 6 October 2026 | 6 October 2026 | [Session Manager](https://000058.awsstudygroup.com/)<br>[AWS CloudFormation](https://000037.awsstudygroup.com/)<br>[AWS CDK Advanced](https://000076.awsstudygroup.com/) |
| Wednesday | Combine IaC, EC2 right-sizing, and VPC Flow Logs. | 7 October 2026 | 7 October 2026 | [Infrastructure as Code Workshop Series](https://000102.awsstudygroup.com/)<br>[EC2 Resource Optimization](https://000032.awsstudygroup.com/)<br>[VPC Flow Logs](https://000074.awsstudygroup.com/) |

### Achievements

- Learned to use consistent tags to identify ownership, environment, application, and cost allocation, and to group related resources.
- Reviewed tag-based access control and its limitations: policies must be designed carefully and not every service action is governed by resource tags in the same way.
- Explored Systems Manager capabilities such as Run Command and Patch Manager for operating and maintaining managed instances.
- Understood the importance of the required IAM permissions, instance role, and managed-node connectivity for Systems Manager operations.
- Reviewed Session Manager as a controlled way to access supported instances without relying on an inbound SSH/RDP port being open to the internet, when prerequisites are configured.
- Learned that CloudFormation models infrastructure as templates and manages deployed resources as stacks, improving repeatability and change tracking.
- Compared declarative templates with AWS CDK, which lets infrastructure be defined using familiar programming languages and synthesized into CloudFormation.
- Recognized that infrastructure code should be reviewed, parameterized, permission-scoped, and cleaned up deliberately.
- Consolidated the IaC workflow: define infrastructure in source-controlled files, review changes, deploy repeatably, and detect configuration drift where supported.
- Learned that EC2 right-sizing should consider actual CPU, memory, network, and workload patterns rather than choosing the largest instance by default.
- Reviewed VPC Flow Logs as records of network traffic metadata used to investigate connectivity and traffic patterns; they do not capture packet payloads.
- Summarized the week’s operations theme: observe behavior, manage resources consistently, and optimize based on evidence.

### Lessons Learned

- Tags and templates make environments repeatable and easier to clean up.
- Visibility into network traffic speeds up troubleshooting.

### Next Steps

Study governance, cost control, and security building blocks in Week 5.
