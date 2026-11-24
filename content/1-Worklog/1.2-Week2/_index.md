---
title: "Week 2 Worklog"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

During Week 2 (21–25 September 2026), i studied compute, object storage, and managed databases, and compared hosting and scaling options.

### Week 2 Objectives

- Understand compute (EC2, IAM roles for EC2), object storage (S3), and managed relational databases (RDS).
- Compare simplified hosting, container deployment, and automatic scaling.

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | Study EC2 fundamentals and IAM roles for EC2. | 21 September 2026 | 21 September 2026 | [Amazon EC2](https://000004.awsstudygroup.com/)<br>[IAM Roles for EC2](https://000048.awsstudygroup.com/) |
| Tuesday | Review cloud development tools, object storage, and managed relational databases. | 22 September 2026 | 22 September 2026 | [AWS Cloud9](https://000049.awsstudygroup.com/)<br>[Amazon S3](https://000057.awsstudygroup.com/)<br>[Amazon RDS](https://000005.awsstudygroup.com/) |
| Wednesday | Compare simplified hosting, container deployment, and automatic scaling. | 23 September 2026 | 23 September 2026 | [Amazon Lightsail](https://000045.awsstudygroup.com/)<br>[Lightsail Containers](https://000046.awsstudygroup.com/)<br>[EC2 Auto Scaling](https://000006.awsstudygroup.com/) |

### Achievements

- Reviewed EC2 instances, AMIs, instance types, key pairs, EBS storage, public IP addressing, and common connection methods.
- Learned how security groups and subnet routing affect whether an instance can be reached.
- Understood why an EC2 instance profile/IAM role is preferred for application access to AWS APIs instead of placing long-lived access keys on the server.
- Noted the importance of selecting appropriately sized resources and terminating unneeded instances to control cost.
- Reviewed the purpose of a cloud development environment and the role of command-line tools when working with AWS resources.
- Learned S3 bucket/object concepts and common static-asset or file-storage use cases, including the need to configure access deliberately.
- Compared a managed relational database in Amazon RDS with operating a database server manually, including the value of managed backups, maintenance, and availability options.
- Connected the service choices to application needs: files and static objects in S3, relational data in RDS, and application code in a compute environment.
- Compared Lightsail’s simplified hosting experience with configuring individual AWS infrastructure components directly.
- Reviewed container-based deployment in Lightsail and the value of packaging an application with its runtime dependencies.
- Learned the purpose of EC2 Auto Scaling groups: maintain a desired number of instances and adjust capacity according to configured policies or health checks.
- Summarized the first-week foundation: account and cost awareness, access management, basic networking, compute, and storage.

### Lessons Learned

- Instance roles are safer than storing credentials on a server.
- Choose the simplest service that meets the requirement before moving to more flexible ones.

### Next Steps

Move on to monitoring, DNS, NoSQL, and a first look at Lambda in Week 3.
