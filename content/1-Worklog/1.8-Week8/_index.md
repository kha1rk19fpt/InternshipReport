---
title: "Week 8 Worklog"
date: 2026-11-02
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:
- Strengthen advanced networking skills: VPC Endpoints (PrivateLink), VPC Peering and centralized network management with Transit Gateway Network Manager.
- Practice deploying a real web application (WordPress) with a highly available architecture (ASG, ALB, RDS, CloudFront).
- Master the "Infrastructure as Code" (IaC) mindset with AWS CloudFormation to automate infrastructure provisioning.
- Agree on how forecasts are displayed, design the DynamoDB table and build the price ingestion pipeline for CPAWS.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Team project meeting: decide how to display forecasts and design the data**<br>+ The web shows q10/q50/q90 converted to price ranges, the probability of a rise, and switches USD/VND<br>+ Designed the DynamoDB `Forecasts` table (PK `asset`, SK `base_date`)<br>+ Finalized the price sources: GLD, SLV, BZ=F, CL=F, VND=X; cross-checked with EIA | 02/11/2026 | 02/11/2026 | [yfinance](https://github.com/ranaroussi/yfinance)<br>[EIA Open Data API](https://www.eia.gov/opendata/) |
| **Tue** | **- Continue Lab 17:**<br>+ VPC Endpoints for AWS services<br>* Deploy a VPC Endpoint<br>* Test the VPC Endpoint<br>+ VPC Endpoint Services<br>* Deploy VPC Endpoint Services<br>+ VPC Peering<br>* VPC Peering Request<br>* Routing configuration<br>* Test VPC Peering<br>+ Transit Gateway Network Manager<br>* Set up Network Manager<br>* Configure Sites<br>* Network Insights<br>+ Clean up resources | 03/11/2026 | 03/11/2026 | [VPC Endpoint](https://000092.awsfcaj.com/vi/6-vpcendpointaws/)<br>[PrivateLink](https://000092.awsfcaj.com/vi/7-vpcendpointonpremise/)<br>[Peering](https://000092.awsfcaj.com/vi/8-vpcpeering/)<br>[Network Manager](https://000092.awsfcaj.com/vi/9-transitgatewaynetworkmanager/) |
| **Wed** | **- Practice Lab 18: Deploy WordPress on AWS Cloud**<br>+ Introduction<br>+ Preparation<br>* Prepare the VPC and Subnets<br>* Create a Security Group for EC2<br>* Create a Security Group for the Database Instance<br>* Launch an EC2 Instance<br>* Launch a Database Instance<br>+ Install WordPress on EC2<br>+ Set up Auto Scaling for the WordPress instance<br>* Create an AMI from the web server instance<br>* Create a Launch Template<br>* Create a Target Group<br>* Create a Load Balancer<br>* Create an Auto Scaling Group<br>+ Database backup and restore<br>* Create a DB snapshot<br>* Restore from the DB snapshot<br>+ Create CloudFront for the web server<br>+ Clean up resources | 04/11/2026 | 04/11/2026 | [Lab 18 Intro](https://000101.awsfcaj.com/vi/1-introduce/)<br>[Install WP](https://000101.awsfcaj.com/vi/3-installwordpressonec2/)<br>[ASG Setup](https://000101.awsfcaj.com/vi/4-asgforec2/)<br>[CloudFront](https://000101.awsfcaj.com/vi/6-createcloudfront/) |
| **Thu** | **- Team project meeting: build the price ingestion pipeline following the data design**<br>+ The `ingest_prices` CLI writes data to S3 (`raw/`, `clean/prices/`) using the NYSE/CME trading calendars<br>+ Drop the current day's bar by New York time, flag abnormal rows, never forward-fill the target<br>+ Packaged the Lambda ingest as a container image with a pinned base image digest | 05/11/2026 | 05/11/2026 | [pandas_market_calendars](https://github.com/rsheftel/pandas_market_calendars)<br>[Lambda Container Image](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html) |
| **Fri** | **- Practice Lab 19: AWS CloudFormation**<br>+ Introduction<br>+ Preparation<br>* Create an IAM User<br>* Create an IAM Role<br>+ CloudFormation basics<br>* Create a workspace<br>* Create a CloudFormation Template<br>+ Advanced CloudFormation<br>* Custom resources<br>Create a Lambda function<br>Create a stack<br>Connect to the EC2 Instance<br>* Mappings and StackSets<br>* Drift Detection<br>+ Clean up resources | 06/11/2026 | 06/11/2026 | [Lab 19 Intro](https://000037.awsfcaj.com/vi/1-introduce/)<br>[CF Basic](https://000037.awsfcaj.com/vi/3-cloudformationbasic/)<br>[CF Advanced](https://000037.awsfcaj.com/vi/4-cloudformationadvance/) |

### Week 8 Achievements:
1. Deployed secure network connectivity: VPC Endpoints (private access to AWS services) and VPC Peering (between VPCs).
2. Built a complete WordPress system with Auto Scaling, load balancing (ALB) and global content delivery (CloudFront).
3. Mastered RDS backup & restore with snapshots in a real scenario.
4. Became proficient with AWS CloudFormation: writing templates (Infrastructure as Code), managing stacks and detecting configuration drift.
5. Completed the daily price ingestion pipeline for 4 assets and the exchange rate, with trading calendars, data flags, running on Lambda.
