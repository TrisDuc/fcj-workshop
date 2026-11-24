---
title: "Week 3 Worklog"
date: 2026-09-28
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

During Week 3 (28 September–2 October 2026), i covered monitoring, DNS, CLI operations, NoSQL, caching, and content delivery, and met AWS Lambda for the first time.

### Week 3 Objectives

- Learn monitoring, DNS, CLI operations, NoSQL, caching, and content delivery.
- Get a first look at AWS Lambda and advanced monitoring — the building blocks later used in the AskFirst project.

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | Learn CloudWatch, Route 53, and AWS CLI. | 28 September 2026 | 28 September 2026 | [Amazon CloudWatch](https://000008.awsstudygroup.com/)<br>[Amazon Route 53](https://000010.awsstudygroup.com/)<br>[AWS CLI](https://000011.awsstudygroup.com/) |
| Tuesday | Explore DynamoDB, ElastiCache, and CloudFront. | 29 September 2026 | 29 September 2026 | [Amazon DynamoDB](https://000060.awsstudygroup.com/)<br>[Amazon ElastiCache](https://000061.awsstudygroup.com/)<br>[Amazon CloudFront](https://000094.awsstudygroup.com/) |
| Wednesday | Study AWS networking and edge compute. | 30 September 2026 | 30 September 2026 | [Networking on AWS Workshop](https://000092.awsstudygroup.com/)<br>[CloudFront and Lambda@Edge](https://000130.awsstudygroup.com/) |
| Thursday | Practice serverless automation and advanced monitoring. | 1 October 2026 | 1 October 2026 | [AWS Lambda](https://000022.awsstudygroup.com/)<br>[CloudWatch and Grafana](https://000029.awsstudygroup.com/)<br>[CloudWatch Advanced Workshop](https://000036.awsstudygroup.com/) |

### Achievements

- Learned to use CloudWatch metrics, logs, alarms, and dashboards to observe resource and application behavior.
- Reviewed Route 53 DNS concepts, including hosted zones and DNS records, and how DNS directs users to application endpoints.
- Practiced the purpose of AWS CLI as a scriptable interface for querying and managing resources; commands use the credentials and default Region configured for the active profile.
- Connected the operations workflow: use CLI to inspect resources, CloudWatch to observe behavior, and DNS to route users to the correct endpoint.
- Reviewed DynamoDB tables, primary keys, query patterns, read/write capacity modes, and secondary indexes; access patterns should guide key design.
- Learned how ElastiCache provides in-memory caching to reduce repeated database work and latency, while requiring a suitable cache invalidation strategy.
- Studied CloudFront distributions, origins, caching behavior, and edge delivery as ways to reduce latency and reduce direct load on an origin.
- Compared the roles of persistent database storage, transient cached data, and content delivery caching.
- Reviewed how VPC components and routing rules work together to connect workloads securely.
- Studied CloudFront and Lambda@Edge at a conceptual level: edge functions can execute selected logic closer to users in supported request/response phases.
- Learned to decide whether logic belongs in the application origin or at the edge based on latency, request handling, and supported runtime constraints.
- Recorded that networking workshops may create billable resources, so lab architecture and cleanup steps need attention.
- Learned the event-driven execution model of Lambda: a function runs in response to an event or invocation without the learner managing the underlying server fleet.
- Reviewed function configuration, permissions, logs, error handling, and the need to make event processing observable.
- Explored CloudWatch/Grafana dashboards as a way to bring metrics into visual panels and monitor trends.
- Learned that metrics, logs, alarms, and dashboards answer different operational questions and are more useful when designed around meaningful signals.

### Lessons Learned

- Metrics and logs should exist before an incident, not after.
- Lambda removes server management, but timeouts, memory, and permissions still need design.

### Next Steps

Practice operations and infrastructure as code in Week 4.
