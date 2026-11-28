---
title: "Week 2 Worklog"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:
- Understand and practice secure user access management with AWS IAM.
- Master the Amazon VPC network architecture and network security layers (Security Groups, NACL).
- Implement Hybrid Cloud connectivity through AWS Site-to-Site VPN.
- Consolidate the networking knowledge learned and kick off the CPAWS team project.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Practice Lab 4: Access management with AWS Identity and Access Management (IAM)**<br>+ IAM User and IAM Group<br>+ IAM Role<br>+ Switching roles<br>**- Practice Lab 5: Getting started with Amazon Virtual Private Cloud (VPC) and AWS Site-to-Site VPN**<br>+ Introduction to Amazon VPC:<br>* Subnets<br>* Route Table<br>* Internet Gateway<br>* NAT Gateway<br>+ Network security with Security Groups and Network ACLs:<br>* Security groups<br>* Network ACLs<br>* VPC Resource Map | 21/09/2026 | 21/09/2026 | [Lab 4 Guide](https://000002.awsfcaj.com/vi/)<br>[Lab 5 Intro](https://000003.awsfcaj.com/vi/1-introduce/)<br>[Lab 5 Firewall](https://000003.awsfcaj.com/vi/2-firewallinvpc/) |
| **Tue** | - Went to the office for the first time and talked with the mentor.<br>- Met, got to know and formed a team with other members of the FCAJ program.<br>**- Continue Lab 5:**<br>+ Prepare the environment:<br>* Create a VPC<br>* Create a Subnet<br>* Create an Internet Gateway<br>* Create a Route Table<br>* Create a Security Group<br>* Enable VPC Flow Logs<br>+ Deploy Amazon EC2 Instances<br>* Create EC2 Instances<br>* Test connection methods<br>* Create a Multi-AZ NAT Gateway<br>* Use Reachability Analyzer<br>* Configure an EIC Endpoint<br>* AWS Systems Manager Session Manager<br>* CloudWatch Monitoring & Alerting | 22/09/2026 | 22/09/2026 | [Lab 5 Prerequisite](https://000003.awsfcaj.com/vi/3-prerequisite/)<br>[Lab 5 Create EC2](https://000003.awsfcaj.com/vi/4-createec2server/) |
| **Wed** | **- Continue Lab 5:**<br>+ Set up AWS Site-to-Site VPN:<br>* Create the VPN environment<br>Create a VPC for the VPN<br>Create an EC2 Instance<br>* Configure the VPN connection<br>Create a Virtual Private Gateway<br>Create a Customer Gateway<br>Create the VPN connection<br>Configure the Customer Gateway<br>Customize the AWS VPN Tunnel<br>Advanced VPN configuration | 23/09/2026 | 23/09/2026 | [Lab 5 VPN](https://000003.awsfcaj.com/vi/5-vpnsitetosite/) |
| **Thu** | **- Consolidate notes from Lab 4 and Lab 5**<br>+ Redraw the VPC diagram (Subnet, Route Table, IGW, NAT Gateway) and the Site-to-Site VPN flow on draw.io<br>+ Review the differences between Security Groups and Network ACLs | 24/09/2026 | 24/09/2026 | [Lab 5 VPN](https://000003.awsfcaj.com/vi/5-vpnsitetosite/) |
| **Fri** | **- Team project meeting: brainstorm, analyze and research to select a suitable idea**<br>+ The CPAWS team (5 members) agreed on how to work and the communication channels<br>+ Proposed forecasting prices of commodities traded both globally and in Vietnam (gold, silver, crude oil) | 25/09/2026 | 25/09/2026 | [World Bank Commodity Markets](https://www.worldbank.org/en/research/commodity-markets) |

### Week 2 Achievements:
1. Went to the AWS office for the first time.
2. Completed Lab 4 and became proficient at creating IAM Users, Groups and Roles for proper access control.
3. Successfully built a VPC network with all components: Subnet, Route Table, IGW, NAT Gateway.
4. Configured multi-layer network security using Security Groups and Network ACLs.
5. Deployed and monitored EC2 Instances, configured secure access via Session Manager and EIC Endpoint.
6. Successfully set up an AWS Site-to-Site VPN connection between AWS and a simulated on-premises environment.
7. Formed the CPAWS team and came up with initial project ideas, including forecasting gold, silver and crude oil prices.
