---
title: "Week 6 Worklog"
date: 2026-10-19
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### OBJECTIVES IN WEEK 6:
- Understand and apply the Serverless architecture (AWS Lambda) to automate operations (cost optimization) and handle events (event-driven).
- Master how Lambda interacts with storage (S3) and the NoSQL database (DynamoDB).
- Master the AWS CLI to manage resources quickly and professionally instead of using only the Console.
- Finalize the solution architecture, the cost estimate and the price data for the CPAWS project.

### TASKS OF WEEK 6:

| Day | Task | Start Date | End Date | References |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Team project meeting: define and research the solution architecture for the web app**<br>+ Chose a scheduled serverless architecture: EventBridge Scheduler -> Lambda ingest -> S3 data lake -> Lambda predict -> DynamoDB -> API Gateway -> web<br>+ Loaded gold, silver, Brent, WTI prices and the USD/VND rate from 01/2012 to S3 and ran EDA (return distribution for h = 1, 3, 7; missing days; negative WTI price on 20/4/2020) | 19/10/2026 | 19/10/2026 |  |
| **Tue** | **- Practice Lab 13: Optimize EC2 costs with Lambda**<br>+ Introduction<br>+ Preparation<br>* Create a VPC<br>* Create a Security Group<br>* Create an EC2 instance<br>* Slack incoming web-hooks<br>+ Create tags for the instance<br>+ Create a role for Lambda<br>+ Create the Lambda functions<br>* Function to stop instances<br>* Function to start instances<br>+ Check the results<br>+ Clean up resources | 20/10/2026 | 20/10/2026 | [Lab 13 Intro](https://000022.awsfcaj.com/vi/1-introduce/)<br>[Create Role](http://000022.awsfcaj.com/vi/4-createroleforlambda/)<br>[Lambda Function](https://000022.awsfcaj.com/vi/5-createlambdafunction/) |
| **Wed** | **- Practice Lab 14: Serverless - Lambda interacting with S3 and DynamoDB**<br>+ Introduction<br>+ Process and optimize image sizes on AWS<br>* Create the image processing Lambda function<br>* Create an S3 bucket<br>* Create an IAM policy for the Lambda function<br>* Test the Lambda function<br>+ Write data to Amazon DynamoDB<br>* Create and manage tables in AWS DynamoDB<br>* Create a Lambda function to write data<br>+ Clean up resources | 21/10/2026 | 21/10/2026 | [Lab 14 Intro](https://000078.awsfcaj.com/vi/1-introduce/)<br>[Resize Function](https://000078.awsfcaj.com/vi/2-resize-image-function/)<br>[DynamoDB Write](https://000078.awsfcaj.com/vi/3-write-data-to-dynaomodb/) |
| **Thu** | **- Team project meeting: finalize the solution architecture and budget estimate for the web app**<br>+ Estimated costs with the AWS Pricing Calculator (about 15–35 USD/month, within the 200 USD credit), no 24/7 endpoint or NAT Gateway<br>+ Tried sliding-window CEEMDAN, measured run time and cached the IMFs for 2014–2019 | 22/10/2026 | 22/10/2026 | [AWS Pricing Calculator](https://calculator.aws/#/)<br>[PyEMD (CEEMDAN)](https://pyemd.readthedocs.io/) |
| **Fri** | **- Practice Lab 15: Getting started with the AWS CLI**<br>+ Introduction<br>+ Preparation<br>+ Install the AWS CLI<br>+ Check resources via the CLI<br>+ AWS CLI with Amazon S3<br>+ AWS CLI with Amazon SNS<br>+ AWS CLI with IAM<br>+ AWS CLI with VPC<br>* AWS CLI with VPC<br>* AWS CLI with Internet Gateway<br>+ Create EC2 with the AWS CLI<br>+ Troubleshooting<br>+ Clean up resources | 23/10/2026 | 23/10/2026 | [Lab 15 Intro](https://000011.awsfcaj.com/vi/1-introduce/)<br>[Install CLI](https://000011.awsfcaj.com/vi/3-installcli/)<br>[CLI with EC2](https://000011.awsfcaj.com/vi/9-ec2/) |

### ACHIEVEMENTS IN WEEK 6:
1. Deployed an automated cost-saving solution (Lab 13): used Lambda to stop/start EC2 on a schedule with Slack notifications.
2. Built a serverless image processing application (Lab 14): combined Lambda, S3 and DynamoDB to resize images automatically and store metadata.
3. Finalized the serverless architecture design and the cost estimate for the CPAWS project.
4. Prepared a clean price dataset from 2012 to date for 4 assets with an EDA report, ready for model building.
5. Became proficient with the AWS CLI (Lab 15) for the core services (S3, EC2, VPC, IAM) without the Console.
