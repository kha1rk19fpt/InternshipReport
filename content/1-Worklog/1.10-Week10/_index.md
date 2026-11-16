---
title: "Week 10 Worklog"
date: 2026-11-16
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### OBJECTIVES IN WEEK 10:
- Build signal features (CEEMDAN, Fed/EIA news, GPR, OVX/GVZ) without future data leakage for the CPAWS model.
- Master the end-to-end Serverless architecture: from event processing (S3 triggers) and storage (DynamoDB) to building APIs (API Gateway).
- Build an automated CI/CD process for Serverless applications with AWS CodePipeline.
- Practice in-depth monitoring and tracing of Serverless applications with CloudWatch and X-Ray.

### TASKS OF WEEK 10:

| Day | Task | Start Date | End Date | References |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Team project meeting: develop signal features for the model:**<br>- Sliding-window CEEMDAN features on the price series, using the IMF cache<br>- News signals (Fed statements, EIA reports), GPR, OVX/GVZ aligned to publication time<br>- Write future-leakage tests for the feature functions | 16/11/2026 | 16/11/2026 | [PyEMD (CEEMDAN)](https://pyemd.readthedocs.io/)<br>[FOMC Statements](https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm)<br>[GPR Index](https://www.matteoiacoviello.com/gpr.htm) |
| **Tue** | **- Practice Lab 22: Serverless - Lambda interacting with S3 and DynamoDB**<br>+ Introduction<br>+ Process and optimize image sizes on AWS<br>* Create the image processing Lambda function<br>* Create an S3 bucket<br>* Create an IAM policy for the Lambda function<br>* Test the Lambda function<br>+ Write data to Amazon DynamoDB<br>* Create and manage tables in AWS DynamoDB<br>* Create a Lambda function to write data<br>+ Clean up resources | 17/11/2026 | 17/11/2026 | [Lab 22 Intro](https://000078.awsfcaj.com/vi/1-introduce/)<br>[Resize Function](https://000078.awsfcaj.com/vi/2-resize-image-function/)<br>[DynamoDB Write](https://000078.awsfcaj.com/vi/3-write-data-to-dynaomodb/) |
| **Wed** | **- Practice Lab 23: Serverless - Writing a front-end that calls API Gateway**<br>+ Introduction to API Gateway / DynamoDB<br>+ Deploy the front-end<br>+ Deploy the Lambda functions<br>* Create a table in DynamoDB<br>* Deploy the Lambda functions<br>Lambda function to write data<br>Lambda function to read data<br>Lambda function to delete data<br>+ Set up API Gateway<br>* Create the methods<br>* Configure and enable CORS<br>+ Test the API with Postman<br>+ Test the API with the front-end<br>+ Clean up resources | 18/11/2026 | 18/11/2026 | [Lab 23 Intro](https://000079.awsfcaj.com/vi/1-introduction/)<br>[Deploy Frontend](https://000079.awsfcaj.com/vi/2-front-end-deployment/)<br>[API Gateway](https://000079.awsfcaj.com/vi/4-config-api-gw/)<br>[Postman Test](https://000079.awsfcaj.com/vi/5-test-api-by-postman/) |
| **Thu** | **- Practice Lab 24: Serverless - CI/CD with CodePipeline**<br>+ Preparation<br>+ Build the SAM pipeline<br>* Create a Git repository<br>* Create the SAM pipeline<br>+ Build the front-end pipeline<br>* Create a Git repository<br>* Create the pipeline<br>+ Test the website<br>+ Clean up | 19/11/2026 | 19/11/2026 | [Lab 24 Intro](https://000084.awsfcaj.com/vi/1-preparation/)<br>[SAM Pipeline](https://000084.awsfcaj.com/vi/2-build-sam-pipeline/) |
| **Fri** | **- Practice Lab 25: Serverless - Monitoring Serverless applications with CloudWatch and X-Ray**<br>+ Preparation<br>+ Monitoring with CloudWatch<br>* Debug with CloudWatch logs<br>* Create custom metrics<br>* Create alarms with CloudWatch Alarm<br>+ Tracing with X-Ray<br>+ Clean up | 20/11/2026 | 20/11/2026 | [Lab 25 Intro](https://000085.awsfcaj.com/vi/1-preparation/)<br>[CloudWatch](https://000085.awsfcaj.com/vi/2-cloudwatch-monitor/)<br>[X-Ray](https://000085.awsfcaj.com/vi/3-x-ray-trace/) |

### ACHIEVEMENTS IN WEEK 10:
1. Completed the shared feature function for training and prediction, with future-leakage tests.
2. Built a complete Serverless backend flow: automated image processing with S3 triggers and NoSQL data management with DynamoDB.
3. Deployed a full-stack Serverless app: connected the front-end to the backend through API Gateway and Lambda, handling CORS correctly.
4. Set up an automated DevOps (CI/CD) process for Serverless applications, making code and front-end deployments faster and less error-prone.
5. Followed and reviewed the CPAWS official test2020 run (16/11): S1a showed statistically significant improvement on silver, WTI and Brent; the champion stays `baseline-q252` as per the locked plan.
