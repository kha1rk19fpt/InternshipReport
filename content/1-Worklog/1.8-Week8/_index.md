---
title: "Week 8 Worklog"
date: 2026-10-25
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### OBJECTIVES IN WEEK 8:
- Complete Advanced Networking skills: VPC Endpoints (PrivateLink), VPC Peering, and centralized network management with Transit Gateway Network Manager.
- Practice deploying a real-world Web Application (WordPress) with High Availability architecture (ASG, ALB, RDS, CloudFront).
- Master the "Infrastructure as Code" (IaC) mindset through AWS CloudFormation to automate infrastructure provisioning.
- Agree on forecast display, design the DynamoDB table, and build the price ingestion pipeline for CPAWS.
- Join the STEM plush toy volunteering activity with AWS HCM.

### TASKS OF WEEK 8:

| Day | Task | Start Date | End Date | References |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Attend event: STEM plush toys with AWS HCM (volunteering, floor 36)**<br>**- Team project meeting: Agree on display and data design**<br>+ The web shows q10/q50/q90 converted into price ranges, probability of an increase, USD/VND toggle<br>+ Designed the DynamoDB `Forecasts` table (PK `asset`, SK `base_date`)<br>+ Settled price sources: GLD, SLV, BZ=F, CL=F, VND=X; cross-checked with EIA | 26/10/2026 | 26/10/2026 | [yfinance](https://github.com/ranaroussi/yfinance)<br>[EIA Open Data API](https://www.eia.gov/opendata/) |
| **Tue** | **- Continue Lab 17 Practice:**<br>+ VPC Endpoints for AWS Services<br>* Deploy VPC Endpoint<br>* Check VPC Endpoint<br>+ VPC Endpoint Services<br>* Deploy VPC Endpoint Services<br>+ VPC Peering<br>* VPC Peering Request<br>* Routing Configuration<br>* Check VPC Peering<br>+ Transit Gateway Network Manager<br>* Setup Network Manager<br>* Configure Site<br>* Network Insights<br>+ Resource Cleanup | 27/10/2026 | 27/10/2026 | [VPC Endpoint](https://000092.awsstudygroup.com/vi/6-vpcendpointaws/)<br>[PrivateLink](https://000092.awsstudygroup.com/vi/7-vpcendpointonpremise/)<br>[Peering](https://000092.awsstudygroup.com/vi/8-vpcpeering/)<br>[Network Manager](https://000092.awsstudygroup.com/vi/9-transitgatewaynetworkmanager/) |
| **Wed** | **- Practice Lab 18: Deploy Wordpress on AWS Cloud**<br>+ Introduction<br>+ Preparation Steps<br>* Prepare VPC and Subnet<br>* Create Security Group for EC2<br>* Create Security Group for Database Instance<br>* Launch EC2 Instance<br>* Launch Database Instance<br>+ Install WordPress on EC2<br>+ Setup Autoscaling for WordPress Instance<br>* Create AMI from Webserver Instance<br>* Create Launch Template<br>* Create Target Group<br>* Create Load Balancer<br>* Create Auto Scaling Group<br>+ Database Backup and Restore<br>* Create DB snapshot<br>* Restore from DB snapshot<br>+ Setup Cloudfront for Web Server<br>+ Resource Cleanup | 28/10/2026 | 28/10/2026 | [Lab 18 Intro](https://000101.awsstudygroup.com/vi/1-introduce/)<br>[Install WP](https://000101.awsstudygroup.com/vi/3-installwordpressonec2/)<br>[ASG Setup](https://000101.awsstudygroup.com/vi/4-asgforec2/)<br>[CloudFront](https://000101.awsstudygroup.com/vi/6-createcloudfront/) |
| **Thu** | **- Team project meeting: Build the price ingestion pipeline according to the data design**<br>+ `ingest_prices` CLI writes data to S3 (`raw/`, `clean/prices/`) using NYSE/CME trading calendars<br>+ Dropped the current day's bar by New York time, flagged anomalous rows, no forward-filling of the target<br>+ Packaged the ingest Lambda as a container image with a pinned base image digest | 29/10/2026 | 29/10/2026 | [pandas_market_calendars](https://github.com/rsheftel/pandas_market_calendars)<br>[Lambda Container Image](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html) |
| **Fri** | **- Practice Lab 19: AWS CloudFormation**<br>+ Introduction<br>+ Preparation Steps<br>* Create IAM User<br>* Create IAM Role<br>+ CloudFormation Basics<br>* Create Workspace<br>* Create CloudFormation Template<br>+ CloudFormation Advanced<br>* Custom Resources<br>Create Lambda Function<br>Create Stack<br>Connect EC2 Instance<br>* Mappings and Stacksets<br>* Drift Detection<br>+ Resource Cleanup | 30/10/2026 | 30/10/2026 | [Lab 19 Intro](https://000037.awsstudygroup.com/vi/1-introduce/)<br>[CF Basic](https://000037.awsstudygroup.com/vi/3-cloudformationbasic/)<br>[CF Advanced](https://000037.awsstudygroup.com/vi/4-cloudformationadvance/) |

### ACHIEVEMENTS IN WEEK 8:
1. Successfully deployed secure network connections: VPC Endpoints (private connection to AWS services) and VPC Peering (connection between VPCs).
2. Built a complete WordPress system with Auto Scaling, Load Balancing (ALB), and global content delivery (CloudFront).
3. Mastered RDS Database Backup & Restore via Snapshots in a real-world scenario.
4. Proficient in using AWS CloudFormation to write Templates (Infrastructure as Code), manage Stacks, and detect Drift.
5. Completed the daily price ingestion pipeline for 4 assets and the exchange rate, with trading calendars, data flags, and running on Lambda.
6. Contributed to the STEM plush toy volunteering activity for children with AWS HCM.
