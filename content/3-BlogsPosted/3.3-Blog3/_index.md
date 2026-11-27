---
title: "Blog 3 - Cost-optimized IoT Data Pipeline: MQTT → AWS IoT Core → Glue → S3"
date: 2026-11-26
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

### Publication Details

- **Author:** Ngo Thi Kim Ngan (Facebook display name: Ngô Thị Kim Ngân), a member of our team.
- **Published:** 26 November 2026, at 10:44 p.m. as shown in the supplied screenshot.
- **Published in:** AWS Study Group VN on Facebook.
- **Original post:** TBD — paste the link of the Facebook post here.

### Designing a Cost-optimized IoT Data Flow for Small Projects (MQTT → AWS IoT Core → Glue → S3)

**Scenario:** A weather-station project with ESP32 or Raspberry Pi sensors usually stops at sending data over MQTT to a dashboard. When it runs 24/7 for months, millions of tiny JSON messages inflate API request costs, slow queries, and make raw data hard to analyze later. The article shows a serverless design for collecting and optimizing long-term sensor data at very low cost for student-scale projects.

{{< event-photo src="images/blogs/iot-parquet/architecture.png" alt="ESP32 or Raspberry Pi sensors send MQTT over TLS to AWS IoT Core, whose rule engine writes JSON to an S3 raw bucket; an EventBridge Scheduler daily trigger runs an AWS Glue job that converts JSON to Parquet in an S3 curated bucket queried by Athena; an S3 lifecycle policy expires raw JSON after 7 days." caption="The blog's architecture: IoT Core rule to S3 raw, daily Glue conversion to Parquet, and Athena queries." >}}

#### The Problem: The "Small Files Nightmare"
If one device sends a JSON message of about 200 bytes every 10 seconds, ten devices over three months produce roughly 7.7 million small files on S3, according to the article's example.

- **API request cost:** S3 charges per PUT and GET request, so millions of small files can cost more in requests than in storage.
- **Slow and costly queries:** Amazon Athena must open each JSON file, which is slow and scans more data.

#### Architecture: Hot and Cold Stages
- **Step 1 — Route from IoT Core to S3 by time partition.** Instead of EC2 or Lambda just to receive MQTT, use the IoT Core Rule Engine to filter with SQL and write straight to the S3 raw bucket in a path like `raw-data/year=YYYY/month=MM/day=DD/hour=HH/`.
- **Step 2 — Compact and convert to Parquet with AWS Glue.** At the end of each day, Amazon EventBridge Scheduler triggers a Glue job (PySpark or Python Shell) that reads the previous day's JSON, cleans types, coalesces the data, and writes Snappy-compressed Apache Parquet to an S3 curated bucket.
- **Step 3 — Automatic cleanup with an S3 Lifecycle policy.** Expire raw JSON after 7 days, keeping a one-week backup for debugging without permanent storage cost.

#### Results in the Article's Scenario (10 million messages per month, about 2 GB of raw JSON)
| Measure | Before | After |
| --- | --- | --- |
| S3 storage | ~2.0 GB | ~280 MB (about 85% less) |
| Number of files | 10,000,000 | ~30 (one per day) |
| Data scanned by Athena for one column | 2.0 GB | ~40 MB (about 98% less) |

These figures are the author's illustrative estimates for the scenario, not measurements from a production system.

#### Tips to Avoid Overspending on AWS Glue
- With Spark, choose worker type G.1X and limit the job to 2 workers.
- For small data (under a few hundred MB per day), use an AWS Glue Python Shell job, which uses 0.0625 DPU, together with the `awswrangler` library to convert JSON to Parquet in seconds.

#### Takeaways
- Partitioned raw storage, a daily compaction job, and automatic expiry together keep a small project cheap and queryable.
- EventBridge Scheduler is a simple way to run batch work on a fixed daily rhythm without a server.

### Publication Evidence

{{< event-photo src="images/blogs/iot-parquet/facebook-publication.png" alt="Screenshot of Ngo Thi Kim Ngan's AWS Study Group VN post, dated 26 November at 10:44 p.m." caption="Screenshot of the published Blog 3 post in AWS Study Group VN." >}}

### References

- [AWS IoT rules](https://docs.aws.amazon.com/iot/latest/developerguide/iot-rules.html)
- [EventBridge Scheduler User Guide](https://docs.aws.amazon.com/scheduler/latest/UserGuide/what-is-scheduler.html)
- [AWS Glue documentation](https://docs.aws.amazon.com/glue/)
- [Amazon S3 Lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)
