---
title: "Week 5 Worklog"
date: 2026-10-12
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:
- Master the database services on AWS (RDS, Aurora, Redshift, ElastiCache).
- Master static website hosting on S3 combined with CloudFront for performance and security.
- Practice deploying, administering and backing up relational databases with Amazon RDS.
- Build a scalable, highly available architecture with Auto Scaling Groups and a Load Balancer.
- Write the CPAWS project proposal with the team, in charge of the forecasting model and evaluation part.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Study Module 06: Database services on AWS:**<br>+ 06-01: Database concepts review<br>+ 06-02: Amazon RDS & Amazon Aurora<br>+ 06-03: Redshift - ElastiCache | 12/10/2026 | 12/10/2026 | [06-01](https://youtu.be/OOD2RwWuLRw?si=gUgPzH3nVghQiDQC)<br>[06-02](https://www.youtube.com/watch?v=qbrobQZrokY)<br>[06-03](https://youtu.be/UvdiRW34aNI?si=scib1_YZ27xtdRJY) |
| **Tue** | **- Practice Lab 10: Static website hosting with Amazon S3**<br>+ Introduction<br>+ Preparation<br>* Create an S3 bucket<br>* Upload data<br>+ Enable static website hosting<br>+ Configure Block Public Access<br>+ Configure public objects<br>+ Test the website<br>+ Speed up the static website with CloudFront<br>* Block all public access to S3<br>* Configure Amazon CloudFront<br>* Test Amazon CloudFront<br>+ Bucket versioning<br>+ Move objects<br>+ Replicate S3 objects to another region<br>+ Clean up resources<br>+ Notes & best practices | 13/10/2026 | 13/10/2026 | [Lab 10 Intro](https://000057.awsfcaj.com/vi/1-introduce/)<br>[Prerequisite](https://000057.awsfcaj.com/vi/2-prerequiste/)<br>[Static Web](https://000057.awsfcaj.com/vi/3-staticwebsite/)<br>[CloudFront](https://000057.awsfcaj.com/vi/7-cloudfront/)<br>[Versioning](https://000057.awsfcaj.com/vi/8-versioning/) |
| **Wed** | **- Team project meeting: write the proposal, define the problem statement and the main flows of the web app**<br>+ Problem statement: users find it hard to know upcoming prices, hard to understand why prices move, and hard to relate them to domestic prices<br>+ Main flows: view forecasts by asset (USD/VND) and ask the chatbot why prices may rise or fall<br>+ Wrote the model part: forecast log returns instead of price levels, split data by time, evaluation metrics | 14/10/2026 | 14/10/2026 |  |
| **Thu** | **- Practice Lab 11: Database fundamentals with Amazon Relational Database Service (RDS)**<br>+ Introduction<br>+ Preparation<br>* Create a VPC<br>* Create an EC2 Security Group<br>* Create an RDS Security Group<br>* Create a DB Subnet Group<br>+ Create an EC2 instance<br>+ Create an RDS database instance<br>+ Deploy the application<br>+ Backup and restore<br>+ Clean up resources | 15/10/2026 | 15/10/2026 | [Lab 11 Intro](https://000005.awsfcaj.com/vi/1-introduce/)<br>[Create RDS](https://000005.awsfcaj.com/vi/4-create-rds/)<br>[Backup](https://000005.awsfcaj.com/vi/6-backup/) |
| **Fri** | **- Practice Lab 12: Deploy the FCJ Management application with an Auto Scaling Group**<br>+ Introduction<br>+ Preparation<br>* Set up the network infrastructure<br>* Launch an EC2 Instance<br>* Launch a Database Instance with Amazon RDS<br>* Load data into the database<br>* Deploy the web server<br>* Prepare metrics for Predictive scaling<br>+ Create a Launch Template<br>+ Set up the Load Balancer<br>* Create a Target Group<br>* Create the Load Balancer<br>+ Check the results<br>+ Create the Auto Scaling Group<br>+ Test the solutions<br>* Test manual scaling<br>* Test scheduled scaling<br>* Test dynamic scaling<br>* Read the predictive scaling metrics<br>+ Clean up resources | 16/10/2026 | 16/10/2026 | [Lab 12 Intro](https://000006.awsfcaj.com/vi/1-introduction/)<br>[Launch Template](https://000006.awsfcaj.com/vi/3-create-launch-template/)<br>[ASG](https://000006.awsfcaj.com/vi/6-create-auto-scaling-group/)<br>[Test Solutions](https://000006.awsfcaj.com/vi/7-test-solutions/) |

### Week 5 Achievements:
1. Completed Lab 10: built a static website on S3, secured the bucket and sped up global access with Amazon CloudFront.
2. Completed Lab 11: deployed a database instance on RDS, connected the application and performed database backup/restore.
3. Completed Lab 12: built a highly available system with an Auto Scaling Group and Elastic Load Balancer; practiced the scaling strategies (Manual, Scheduled, Dynamic, Predictive).
4. Consolidated the theory of database types on AWS (SQL vs NoSQL, OLTP vs OLAP).
5. Completed the CPAWS proposal draft, with a clear problem statement, the main business flows and the forecasting model approach.
