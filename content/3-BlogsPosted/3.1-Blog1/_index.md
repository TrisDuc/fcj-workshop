---
title: "Blog 1 - Lambda + EventBridge: Event-driven Architecture for Daily Data Updates"
date: 2026-11-21
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

### Publication Details

- **Author:** Vo Duc Tri (Facebook display name: Tri Duc Vo).
- **Published:** 21 November 2026, at 11:21 p.m. as shown in the supplied screenshot.
- **Published in:** AWS Study Group VN on Facebook.
- **Original post:** TBD — paste the link of your Facebook post here.

### Lambda + EventBridge: How I Use Event-driven Architecture to Update Data Every Day

**Scenario:** Every morning the author opened four or five tabs to check fuel prices, gold prices, esports scores, and the weather. The goal was a single page that updates itself, built with AWS Lambda and Amazon EventBridge so that each part reacts only to its own event.

#### Requirements
- **No server running 24/7:** use resources only when there is work.
- **Each source has its own rhythm:** weather changes during the day, esports scores need faster updates during matches, and fuel prices change by period.
- **One failing source must not break the page:** if the gold API is down, the weather must still appear.
- **Easy to add a new source** and a **small personal cost.**

#### What Event-driven Means Here
A single script that fetches everything and builds the page can be affected by one slow or failing API. In an event-driven design, "time to fetch gold prices" calls one Lambda, and "new data is in the store" calls another Lambda that rebuilds the page. The two do not call each other; EventBridge connects them through events, so each part can run, fail, and change independently.

{{< event-photo src="images/blogs/lambda-eventbridge/architecture.png" alt="EventBridge Scheduler invokes a collector Lambda that writes to S3; an S3 Object Created event goes through an EventBridge rule to a renderer Lambda that writes the page to S3, served through CloudFront." caption="The blog's architecture: Scheduler, collector Lambda, S3, EventBridge rule, renderer Lambda, S3, and CloudFront." >}}

#### Architecture
The website is static HTML on Amazon S3, delivered through Amazon CloudFront.

1. **EventBridge Scheduler** acts as the clock. Each source has its own schedule and sends a JSON payload such as `{"source": "gold"}`.
2. **Lambda collector** fetches one source, normalizes it into small JSON, and writes `data/<source>.json` to S3.
3. **EventBridge rule** catches the S3 *Object Created* event for the `data/` prefix.
4. **Lambda renderer** merges the JSON files, rebuilds `index.html`, and invalidates the CloudFront cache.

#### Step 1: One Rhythm per Source with EventBridge Scheduler
Scheduler invokes Lambda asynchronously and supports one-time schedules and recurring `rate` or `cron` schedules. Because each schedule can carry its own payload, one collector function serves every source. Scheduler also supports time zones, so schedules can use Vietnam time. The planned schedules are weather every 3 hours, fuel and gold daily or weekly, and match scores every 30 minutes. Each schedule needs an execution role, and a retry policy plus a dead-letter queue keep failed invocations from disappearing silently.

#### Step 2: The Lambda Collector
The collector reads the source name from the payload, calls the matching API with a timeout, normalizes the result, and writes it to S3. Normalizing here means the renderer does not need to know each source's format. One rule is kept: **if a source fails, the Lambda reports the error and does not overwrite the old file**, so the page keeps the previous data instead of going blank. For data sources, official APIs or sources that allow automated access are preferred, and each site's terms are read carefully.

#### Step 3: Events Connect the Collector and the Renderer
Once the collector writes the file, its job is done; it does not know who uses the file. S3 can send bucket events to EventBridge (enabled under the bucket's Event notifications), and an EventBridge rule matches *Object Created* events for the bucket with the `data/` key prefix. Two points to remember:

- **Filter by prefix `data/`.** The renderer also writes `index.html` into the same bucket. If the rule matched every new object, that write would trigger the renderer again and create an endless loop.
- **Delivery is at-least-once,** so duplicate events are possible. The renderer is **idempotent**: it always rebuilds the page from the current data and overwrites it, so running it twice gives the same result.

CloudFront caches content at the edge, so the renderer creates an invalidation for `/index.html`. Each section of the page shows its source's `updatedAt` value so a stale source is easy to spot.

#### Takeaways
- Small, independent parts: a failure does not spread, and a new source is just a new schedule and normalizer.
- The interesting part is not writing one Lambda but how AWS services connect into a complete system without a server running all the time.

What do you use to trigger your Lambda functions — a schedule, an S3 event, or something else?

### Publication Evidence

{{< event-photo src="images/blogs/lambda-eventbridge/facebook-publication.png" alt="Screenshot of Tri Duc Vo's AWS Study Group VN post, dated 21 November at 11:21 p.m." caption="Author-supplied screenshot of the published Blog 1 post in AWS Study Group VN." >}}

### References

- [Invoke a Lambda function on a schedule – AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/with-eventbridge-scheduler.html)
- [EventBridge Scheduler User Guide](https://docs.aws.amazon.com/scheduler/latest/UserGuide/what-is-scheduler.html)
- [New – Use Amazon S3 Event Notifications with Amazon EventBridge – AWS News Blog](https://aws.amazon.com/blogs/aws/new-use-amazon-s3-event-notifications-with-amazon-eventbridge/)
- Step-by-step guide to CloudFront invalidation in CodePipeline using Lambda – Medium
