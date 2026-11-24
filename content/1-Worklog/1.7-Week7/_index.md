---
title: "Week 7 Worklog"
date: 2026-10-26
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

During Week 7 (26–30 October 2026), i studied CI/CD and cost analytics and built a serverless foundation with Lambda, DynamoDB, API Gateway, and AWS SAM.

### Week 7 Objectives

- Study CI/CD with CodePipeline, cost commitment options, cost analytics with Glue and Athena.
- Build a serverless foundation with Lambda, DynamoDB, Amplify, API Gateway, and AWS SAM.

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | Study CodePipeline delivery workflows and cloud cost commitment options. | 26 October 2026 | 26 October 2026 | [CodePipeline CI/CD](https://000017.awsstudygroup.com/)<br>[Automated CodePipeline Deployments](https://000023.awsstudygroup.com/)<br>[Savings Plans and Reserved Instances](https://000042.awsstudygroup.com/) |
| Tuesday | Explore DevOps pipeline practices, cost visualization, Glue, and Athena. | 27 October 2026 | 27 October 2026 | [ECS Applications CI/CD / DevOps with CodePipeline](https://000152.awsstudygroup.com/)<br>[Cost Visualization and Analytics](https://000034.awsstudygroup.com/)<br>[AWS Glue and Amazon Athena](https://000040.awsstudygroup.com/) |
| Wednesday | Build a serverless CRUD foundation with Lambda and DynamoDB. | 28 October 2026 | 28 October 2026 | [Serverless CRUD with Lambda and DynamoDB](https://000133.awsstudygroup.com/) |
| Thursday | Integrate authentication/storage, API Gateway, and AWS SAM. | 29 October 2026 | 29 October 2026 | [AWS Amplify](https://000134.awsstudygroup.com/)<br>[API Gateway Integration](https://000135.awsstudygroup.com/)<br>[AWS SAM Deployment](https://000136.awsstudygroup.com/) |

### Achievements

- Learned the purpose of a CI/CD pipeline: automate stages such as source retrieval, build, test, approval, and deployment, depending on the pipeline design.
- Compared a pipeline’s orchestration role with the build and deploy actions performed by its configured providers.
- Reviewed Savings Plans and Reserved Instances as commitment-based pricing options whose suitability depends on workload stability, usage pattern, and terms.
- Recognized that delivery automation improves repeatability but still requires permission boundaries, rollback planning, and deployment verification.
- Reviewed a more complete DevOps workflow around application delivery and deployment automation, including the importance of source-controlled definitions and traceable changes.
- Learned to visualize and compare cloud expenditure by service or other available dimensions to identify the largest cost drivers.
- Studied AWS Glue’s role in data cataloging and ETL preparation and Athena’s ability to query data in supported data stores such as S3 using SQL.
- Connected cost and usage data with analytics: reliable conclusions depend on understanding the dataset, partitioning, query scope, and the cost of the analysis itself.
- Reviewed the Document Management System workshop architecture and its use of Lambda functions with DynamoDB for document metadata and application operations.
- Learned to map CRUD actions to function handlers, database operations, input validation, permissions, and error responses.
- Practiced thinking about DynamoDB keys and access patterns before choosing query operations, and about granting a Lambda execution role only the required permissions.
- Recognized that functions, database tables, and related resources should be tested and cleaned up as part of a complete lab workflow.
- Reviewed how Amplify can support frontend integration with authentication and storage capabilities, while Cognito and S3 provide underlying AWS service capabilities in the workshop architecture.
- Learned API Gateway’s role as the managed API entry point that routes requests to backend integrations such as Lambda.
- Studied AWS SAM as a framework for defining and packaging serverless resources, then deploying them through CloudFormation-based infrastructure management.
- Connected authentication, API exposure, Lambda execution, data persistence, and infrastructure deployment into a coherent request flow.

### Lessons Learned

- SAM keeps a serverless application reproducible from a single template.
- DynamoDB key design should start from access patterns.

### Next Steps

Finish the serverless picture and consolidate learning in Week 8.
