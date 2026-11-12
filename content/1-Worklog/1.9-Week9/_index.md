---
title: "Week 9 Worklog"
date: 2026-11-01
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### OBJECTIVES IN WEEK 9:
- Approach and master AWS Cloud Development Kit (CDK) to define cloud infrastructure using programming languages.
- Practice deploying complex architectures (ECS, ALB, API Gateway, Serverless) using CDK and Nested Stacks.
- Reinforce Infrastructure as Code (IaC) mindset by deploying a complete Three-Tier Architecture.
- Evaluate the CPAWS forecasting models on the 2019 validation set and write the predict Lambda code.

### TASKS OF WEEK 9:

| Day | Task | Start Date | End Date | References |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Team project meeting: Build the baseline and evaluate models**<br>+ Statistical baseline: causal empirical quantiles over 252 sessions (`baseline-q252`)<br>+ With Long, compared the DLinear MVP (price only, window 60, 7h × 7q output) against the baseline on the 2019 validation set using `pinball_skill` | 02/11/2026 | 02/11/2026 | [DLinear Paper](https://arxiv.org/abs/2205.13504) |
| **Tue** | **- Practice Lab 19: CDK Basics**<br>+ Introduction<br>+ Preparation Steps<br>* Create IAM Role<br>+ CDK Basics<br>* Create workspace<br>* Configure Cloud9<br>+ Create CDK Template<br>+ Update CDK Template<br>+ Resource Cleanup | 03/11/2026 | 03/11/2026 | [Lab 19 Intro](https://000038.awsstudygroup.com/vi/1-introduce/)<br>[CDK Basic](https://000038.awsstudygroup.com/vi/3-cdkbasic/)<br>[Create Template](https://000038.awsstudygroup.com/vi/4-createcdktemplate/) |
| **Wed** | **- Practice Lab 20: CDK Basics - 2**<br>+ Introduction<br>+ Preparation Steps<br>* Create IAM Role<br>* Launch an EC2 Server<br>* Configure VSCode Environment<br>+ ECS, ALB and API Gateway<br>+ Lambda and S3<br>+ Nested stack<br>* Create nested stacks with CDK<br>+ Resource Cleanup | 04/11/2026 | 04/11/2026 | [Lab 20 Intro](https://000076.awsstudygroup.com/vi/1-introduction/)<br>[ECS & ALB](https://000076.awsstudygroup.com/vi/3-ecs-alb-and-api-gateway/)<br>[Nested Stack](https://000076.awsstudygroup.com/vi/5-nested-stack/) |
| **Thu** | **- Team project meeting: Choose the production model and write the predict Lambda code**<br>+ DLinear lost to the baseline in 28/28 validation groups → the product uses `baseline-q252`, DLinear stays as challenger<br>+ Predict Lambda: read clean data, run the model, `normalize_quantiles()`, write results to DynamoDB | 05/11/2026 | 05/11/2026 | [DynamoDB Query](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html) |
| **Fri** | **- Practice Lab 21: Introduction to Infrastructure as Code**<br>+ Introduction<br>+ Preparation Steps<br>+ Create Lambda function<br>+ Create VPC and EC2<br>+ Deploy Three-Tier Architecture<br>* Deploy CloudFormation Stack<br>* Check Web Tier access<br>* Check Application Tier access<br>* Check Database Tier access<br>+ Resource Cleanup | 06/11/2026 | 06/11/2026 | [Lab 21 Intro](https://000102.awsstudygroup.com/vi/1-introduction/)<br>[Create Lambda](https://000102.awsstudygroup.com/vi/3-createlambda/)<br>[Three Tier](https://000102.awsstudygroup.com/vi/5-threetierweb/) |

### ACHIEVEMENTS IN WEEK 9:
1. Successfully set up the CDK workspace (Cloud9, VSCode) and deployed basic infrastructure Templates.
2. Applied CDK to build advanced resources: ECS Cluster, Application Load Balancer, API Gateway, and Nested Stack structure.
3. Successfully deployed a standard Three-Tier Architecture (Web, App, Database layers) entirely via code (IaC).
4. Completed the 2019 validation evaluation, chose the production model based on evidence, and delivered predict Lambda code writing forecasts to DynamoDB.
