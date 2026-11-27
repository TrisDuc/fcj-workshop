---
title: "Blog 2 - Testing a Serverless Event-driven Flow (SQS + DLQ + DynamoDB) Locally with LocalStack and Java 21"
date: 2026-11-25
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

### Publication Details

- **Author:** Ngo Thi Kim Ngan (Facebook display name: Ngô Thị Kim Ngân), a member of our team.
- **Published:** 25 November 2026, at 9:28 p.m. as shown in the supplied screenshot.
- **Published in:** AWS Study Group VN on Facebook.
- **Original post:** TBD — paste the link of the Facebook post here.

### Test a Serverless Event-driven Flow Locally at No Cost: LocalStack + Java 21

**Scenario:** An asynchronous flow receives an event, puts it on Amazon SQS, and processes it with an AWS Lambda function in Java 21 that writes to DynamoDB and reads Amazon SSM Parameter Store, with an SQS dead-letter queue (DLQ) for failures. Pushing straight to AWS to debug every small error is slow and can create unexpected cost. Our team's backend uses the same setup: Docker with LocalStack for local runs, then AWS SAM for deployment.

{{< event-photo src="images/blogs/localstack/architecture.png" alt="A developer and test script send payloads to a main SQS queue in LocalStack; a Java 21 Lambda handler writes to DynamoDB and reads SSM; failures go to a DLQ; after local verification the same design is deployed with AWS SAM to ap-southeast-1." caption="The blog's architecture: local testing in LocalStack, then deployment to AWS with AWS SAM." >}}

#### The Local-First Approach
The flow is simulated on the developer's machine with LocalStack: SQS, the DLQ, DynamoDB, and SSM. Both the success path and the failure path are tested before building and deploying with AWS SAM.

#### Three Practical Points
- **Keep the endpoint clean in the Java 21 SDK v2.** Call `endpointOverride(URI.create(localEndpoint))` only in `local` mode (`http://localhost:4566`). In the cloud, the default endpoint is used. In the diagram this is the `isLocal` flag: true locally and false on AWS.
- **Test the happy path and the DLQ path.** A valid payload should be written to DynamoDB and removed from the main queue. A payload that causes an error should land in the DLQ after the configured number of retries.
- **Go to the cloud later.** Deploy with AWS SAM once the logic and configuration are stable locally, which avoids an expensive fix-and-redeploy loop in the real environment.

#### Takeaways
- DynamoDB also holds state and idempotency data in this flow, so a message processed twice does not cause a duplicate effect.
- Local tests prove the logic and retry behavior; a final check on AWS still confirms real permissions and quotas.

How do you test event-driven flows: LocalStack, SAM local, or directly on AWS?

### Publication Evidence

{{< event-photo src="images/blogs/localstack/facebook-publication.png" alt="Screenshot of Ngo Thi Kim Ngan's AWS Study Group VN post, dated 25 November at 9:28 p.m." caption="Screenshot of the published Blog 2 post in AWS Study Group VN." >}}

### References

- [LocalStack documentation](https://docs.localstack.cloud/)
- [AWS Serverless Application Model (AWS SAM)](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)
- [Amazon SQS dead-letter queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html)
