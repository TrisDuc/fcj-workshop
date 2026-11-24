---
title: "Week 8 Worklog"
date: 2026-11-02
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

During Week 8 (2–6 November 2026), i completed the serverless view with CDN, search, and tracing, reviewed ECS/Fargate and EKS, and consolidated the learning path before the project.

### Week 8 Objectives

- Complete the serverless application view: CDN, search, serverless DevOps, and distributed tracing.
- Review ECS/Fargate and EKS, then consolidate the eight-week learning path and prepare for the AskFirst project (weeks 9–12).

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | Add CDN delivery, search, and automated deployment to the serverless app. | 2 November 2026 | 2 November 2026 | [CloudFront content delivery](https://000137.awsstudygroup.com/)<br>[Adding Search Functionality](https://000138.awsstudygroup.com/)<br>[Serverless DevOps](https://000139.awsstudygroup.com/) |
| Tuesday | Trace serverless requests and review ECS/Fargate CI/CD. | 3 November 2026 | 3 November 2026 | [X-Ray and CloudWatch tracing](https://000140.awsstudygroup.com/)<br>[ECS and AWS Fargate](https://000067.awsstudygroup.com/)<br>[CI/CD for ECS Applications](https://000152.awsstudygroup.com/) |
| Wednesday | Review EKS, EKS Blueprints for CDK, and Kubernetes CI/CD; consolidate the eight-week learning. | 4 November 2026 | 4 November 2026 | [Amazon EKS](https://000126.awsstudygroup.com/)<br>[EKS Blueprints for CDK](https://000065.awsstudygroup.com/)<br>[CI/CD for EKS Applications](https://000062.awsstudygroup.com/) |

### Achievements

- Reviewed CloudFront, DNS, and TLS-related responsibilities in delivering a web application efficiently and securely to users; certificate and domain configuration must match the deployment architecture.
- Studied the search extension of the document-management application, including the role of a search service and indexing pipeline where configured by the workshop.
- Learned how a CI/CD pipeline can package and deploy serverless application changes consistently rather than relying only on manual console steps.
- Considered operational concerns such as index freshness, deployment failure handling, cache invalidation, and safe configuration of environments.
- Learned how distributed tracing follows a request across supported application components and how X-Ray trace maps help identify latency or failing segments.
- Connected logs, metrics, and traces: each provides different evidence when diagnosing a production issue.
- Reviewed ECS with Fargate as a way to run containers without managing the underlying EC2 server fleet directly, while still defining tasks, networking, permissions, and scaling.
- Studied how a pipeline can build container images, publish them to a registry, and deploy updates to ECS with appropriate validation and rollback considerations.
- Reviewed Amazon EKS as a managed Kubernetes control-plane service and distinguished Kubernetes concepts such as cluster, node, pod, deployment, and service from ECS concepts.
- Studied EKS Blueprints for CDK as an infrastructure pattern/tooling approach for defining repeatable EKS environments and related AWS resources.
- Reviewed a Kubernetes delivery pipeline at a conceptual level: build/test the application, package and publish an image, update deployment configuration, and verify rollout health.
- Consolidated the eight-week learning path (weeks 1–8) across foundational AWS services, operations, security, resilience, cost awareness, serverless modernization, observability, and containers.
- Identified follow-up practice areas: repeat selected labs independently, document architectures, verify IAM permissions, and clean up billable resources after each session.

### Lessons Learned

- Distributed tracing is essential once a request crosses several services.
- Choosing between Lambda and containers depends on workload shape and operational effort.

### Next Steps

Start the AskFirst project in Week 9: requirements, architecture, and foundation.
