---
title: "Week 9 Worklog"
date: 2026-11-09
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:
- Get started with and master the AWS Cloud Development Kit (CDK) to define cloud infrastructure in a programming language.
- Practice deploying complex architectures (ECS, ALB, API Gateway, Serverless) with CDK and Nested Stacks.
- Strengthen the Infrastructure as Code (IaC) mindset by deploying a complete Three-Tier Architecture.
- Evaluate the CPAWS forecasting model on the 2019 validation set and write the Lambda predict code.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Team project meeting: build the benchmark and evaluate the model**<br>+ Statistical benchmark: causal 252-session empirical quantiles (`baseline-q252`)<br>+ With Long, compared DLinear MVP (prices only, window 60, output 7h × 7q) against the benchmark on validation 2019 using `pinball_skill` | 09/11/2026 | 09/11/2026 | |
| **Tue** | **- Practice Lab 19: CDK basics**<br>+ Introduction<br>+ Preparation<br>* Create an IAM Role<br>+ CDK basics<br>* Create a workspace<br>* Configure Cloud9<br>+ Create a CDK Template<br>+ Update the CDK Template<br>+ Clean up resources | 10/11/2026 | 10/11/2026 | [Lab 19 Intro](https://000038.awsfcaj.com/vi/1-introduce/)<br>[CDK Basic](https://000038.awsfcaj.com/vi/3-cdkbasic/)<br>[Create Template](https://000038.awsfcaj.com/vi/4-createcdktemplate/) |
| **Wed** | **- Practice Lab 20: CDK basics - 2**<br>+ Introduction<br>+ Preparation<br>* Create an IAM Role<br>* Run an EC2 server<br>* Configure the VS Code environment<br>+ ECS, ALB and API Gateway<br>+ Lambda and S3<br>+ Nested stack<br>* Create nested stacks with CDK<br>+ Clean up resources | 11/11/2026 | 11/11/2026 | [Lab 20 Intro](https://000076.awsfcaj.com/vi/1-introduction/)<br>[ECS & ALB](https://000076.awsfcaj.com/vi/3-ecs-alb-and-api-gateway/)<br>[Nested Stack](https://000076.awsfcaj.com/vi/5-nested-stack/) |
| **Thu** | **- Team project meeting: decide the production model and write the Lambda predict code**<br>+ DLinear lost to the benchmark in 28/28 validation groups → the product uses `baseline-q252`, DLinear stays as challenger<br>+ Lambda predict: read clean data, run the model, `normalize_quantiles()`, write results to DynamoDB | 12/11/2026 | 12/11/2026 | |
| **Fri** | **- Practice Lab 21: Introduction to Infrastructure as Code**<br>+ Introduction<br>+ Preparation<br>+ Create a Lambda function<br>+ Create a VPC and EC2<br>+ Deploy a Three-Tier Architecture<br>* Deploy the CloudFormation Stack<br>* Test access to the Web Tier<br>* Test access to the Application Tier<br>* Test access to the Database Tier<br>+ Clean up resources | 13/11/2026 | 13/11/2026 | [Lab 21 Intro](https://000102.awsfcaj.com/vi/1-introduction/)<br>[Create Lambda](https://000102.awsfcaj.com/vi/3-createlambda/)<br>[Three Tier](https://000102.awsfcaj.com/vi/5-threetierweb/) |

### Week 9 Achievements:
1. Set up the CDK working environment (Cloud9, VS Code) and deployed basic infrastructure templates.
2. Applied CDK to build advanced resources: ECS Cluster, Application Load Balancer, API Gateway and Nested Stacks.
3. Deployed a standard Three-Tier Architecture (Web, App, Database layers) entirely in code (IaC).
4. Completed the evaluation on validation 2019, chose the production model based on the numbers and wrote the Lambda predict code that writes forecasts to DynamoDB.
