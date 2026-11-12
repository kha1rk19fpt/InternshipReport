---
title: "Week 10 Worklog"
date: 2026-11-08
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### OBJECTIVES IN WEEK 10:
- Build leakage-free signal features (CEEMDAN, Fed/EIA news, GPR, OVX/GVZ) for the CPAWS model.
- Master comprehensive Serverless architecture: From event processing (S3 triggers), storage (DynamoDB) to API building (API Gateway).
- Build automated CI/CD pipelines for Serverless applications using AWS CodePipeline.
- Practice in-depth Monitoring and Tracing for Serverless applications with CloudWatch and X-Ray.

### TASKS OF WEEK 10:

| Day | Task | Start Date | End Date | References |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Team project meeting: Develop signal features for the model:**<br>- Rolling-window CEEMDAN features on price series using the IMF cache<br>- News signals (Fed statements, EIA reports), GPR, OVX/GVZ aligned to publication time<br>- Wrote look-ahead leakage tests for the feature builder | 09/11/2026 | 09/11/2026 | [PyEMD (CEEMDAN)](https://pyemd.readthedocs.io/)<br>[FOMC Statements](https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm)<br>[GPR Index](https://www.matteoiacoviello.com/gpr.htm) |
| **Tue** | **- Practice Lab 22: Serverless - Lambda interaction with S3 and DynamoDB**<br>+ Introduction<br>+ Image Processing and Optimization on AWS<br>* Create Image Processing Lambda Function<br>* Create S3 Bucket<br>* Create IAM Policy for Lambda Function<br>* Test Lambda Function<br>+ Write data to Amazon DynamoDB<br>* Create and Manage Tables in AWS DynamoDB<br>* Create Lambda Function to Write data<br>+ Resource Cleanup | 10/11/2026 | 10/11/2026 | [Lab 22 Intro](https://000078.awsstudygroup.com/vi/1-introduce/)<br>[Resize Function](https://000078.awsstudygroup.com/vi/2-resize-image-function/)<br>[DynamoDB Write](https://000078.awsstudygroup.com/vi/3-write-data-to-dynaomodb/) |
| **Wed** | **- Practice Lab 23: Serverless - Guide to writing Front-end calling API Gateway**<br>+ Introduction to API Gateway / DynamoDB<br>+ Deploy front-end<br>+ Deploy Lambda function<br>* Create table in DynamoDB<br>* Deploy Lambda function<br>Lambda function writes data<br>Lambda function reads data<br>Lambda function deletes data<br>+ Setup API Gateway<br>* Create methods<br>* Configure and enable CORS<br>+ Test API with Postman<br>+ Test API with front-end<br>+ Resource Cleanup | 11/11/2026 | 11/11/2026 | [Lab 23 Intro](https://000079.awsstudygroup.com/vi/1-introduction/)<br>[Deploy Frontend](https://000079.awsstudygroup.com/vi/2-front-end-deployment/)<br>[API Gateway](https://000079.awsstudygroup.com/vi/4-config-api-gw/)<br>[Postman Test](https://000079.awsstudygroup.com/vi/5-test-api-by-postman/) |
| **Thu** | ... | 12/11/2026 | 12/11/2026 | ... |
| **Fri** | **- Practice Lab 24: Serverless - CI/CD with CodePipeline**<br>+ Preparation<br>+ Build SAM pipeline<br>* Create Git repository<br>* Create SAM pipeline<br>+ Build front-end pipeline<br>* Create Git repository<br>* Create pipeline<br>+ Test web operation<br>+ Cleanup<br>**- Practice Lab 25: Serverless - Monitoring Serverless applications with CloudWatch and X-Ray**<br>+ Preparation<br>+ Monitor with CloudWatch<br>* Debug with CloudWatch Logs<br>* Create custom metrics<br>* Create alarms with CloudWatch Alarm<br>+ Trace with X-ray<br>+ Cleanup | 13/11/2026 | 13/11/2026 | [Lab 24 Intro](https://000084.awsstudygroup.com/vi/1-preparation/)<br>[SAM Pipeline](https://000084.awsstudygroup.com/vi/2-build-sam-pipeline/)<br>[Lab 25 Intro](https://000085.awsstudygroup.com/vi/1-preparation/)<br>[CloudWatch](https://000085.awsstudygroup.com/vi/2-cloudwatch-monitor/)<br>[X-Ray](https://000085.awsstudygroup.com/vi/3-x-ray-trace/) |

### ACHIEVEMENTS IN WEEK 10:
1. Completed a shared feature builder for training and prediction, with look-ahead leakage tests.
2. Built a complete Serverless Backend flow: Automated image processing with S3 Triggers and NoSQL data management with DynamoDB.
3. Successfully deployed a Full-stack Serverless App: Connected Frontend with Backend via API Gateway and Lambda, handling CORS properly.
4. Established automated DevOps (CI/CD) for Serverless applications, making Code and Frontend deployment fast and less error-prone.
5. ...
