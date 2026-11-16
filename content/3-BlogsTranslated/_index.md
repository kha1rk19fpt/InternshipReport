---
title: "Blogs Posted"
date: 2026-09-08
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

The three blog posts below were written by me from what I learned about AWS services while working on the CPAWS project, and posted to the [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj) community. Each post uses its own example and a diagram I drew in draw.io.

### [Blog 1 - A Model That Runs a Few Seconds a Day: Do You Need a SageMaker Endpoint?](3.1-Blog1/)

Compares the four SageMaker inference options with an AWS Lambda container image for a nightly forecasting job: zip versus container limits, the 7 flows of the pipeline (ECR, EventBridge Scheduler, Lambda destinations, S3, DynamoDB, CloudWatch), retries for asynchronous invocations, and cost.

### [Blog 2 - Amazon S3 Versioning: Reproducing Data Processing Results When Files Get Overwritten](3.2-Blog2/)

Organizing a layered data lake, how S3 Versioning keeps old versions, pinning input data with VersionId and SHA-256 before an evaluation run, the `s3:GetObjectVersion` permission, and lifecycle rules for old versions.

### [Blog 3 - Designing a DynamoDB Table: Start from the Questions, Not from the Data](3.3-Blog3/)

Choosing the partition key and sort key from the application's questions, reading the latest record with Query `Limit=1`, storing one forecast run per item, and conditional writes so a rerun job never overwrites data.
