---
title: "Week 6 Worklog"
date: 2026-10-19
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

During Week 6 (19–23 October 2026), i studied backup, VPC peering, SQS and SNS messaging, and container fundamentals with Docker and ECS.

### Week 6 Objectives

- Understand backup, VPC peering, and event-driven messaging with SQS and SNS (queues, retries, dead-letter queues).
- Learn container fundamentals: Docker, ECS, and ECS deployment with CDK.

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | Learn backup, VPC peering, and event-driven messaging with SQS and SNS. | 19 October 2026 | 19 October 2026 | [AWS Backup](https://000013.awsstudygroup.com/)<br>[VPC Peering](https://000019.awsstudygroup.com/)<br>[SQS and SNS](https://000077.awsstudygroup.com/) |
| Tuesday | Explore shared EBS storage and highly available operations. | 20 October 2026 | 20 October 2026 | [EBS Multi-Attach](https://100000.awsstudygroup.com/)<br>[EBS Multi-Attach and Systems Manager](https://100001.awsstudygroup.com/) |
| Wednesday | Learn Docker, ECS, and infrastructure deployment for ECS using CDK. | 21 October 2026 | 21 October 2026 | [Docker](https://000015.awsstudygroup.com/)<br>[Amazon ECS](https://000016.awsstudygroup.com/)<br>[ECS with CDK](https://000118.awsstudygroup.com/) |

### Achievements

- Reviewed AWS Backup as a centralized way to define backup plans, schedules, and retention for supported resources.
- Learned that VPC peering connects two VPCs using private IP routing but is not transitive; route tables and security rules still determine reachability.
- Compared SQS queues for decoupled point-to-point processing with SNS topics for publish/subscribe fan-out, including an SNS-to-SQS pattern.
- Recognized the role of retries, visibility timeouts, dead-letter queues, idempotent consumers, and monitoring in reliable asynchronous workflows.
- Learned the purpose and constraints of EBS Multi-Attach for supported io1/io2 volumes and compatible EC2 instances in the same Availability Zone.
- Reviewed why attaching a shared block device does not automatically make concurrent writes safe; the application and cluster filesystem must coordinate access correctly.
- Studied how Systems Manager can support operational tasks and instance management around a shared-storage setup.
- Distinguished shared storage from database replication, backups, and multi-AZ disaster recovery; these solve different availability and data-protection problems.
- Reviewed container images, containers, Dockerfiles, image registries, and the value of repeatable packaging across environments.
- Learned core ECS concepts including clusters, task definitions, tasks, services, and the relationship between a container image and a running workload.
- Compared infrastructure deployment by manual configuration with defining ECS resources using CDK.
- Summarized security and operating considerations such as image provenance, environment configuration, logs, health checks, capacity, and cleaning up resources.

### Lessons Learned

- Queues decouple components, but consumers must handle retries and duplicates.
- A dead-letter queue turns silent failures into something visible.

### Next Steps

Build serverless foundations and CI/CD in Week 7.
