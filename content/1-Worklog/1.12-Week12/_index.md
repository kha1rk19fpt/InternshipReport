---
title: "Week 12 Worklog"
date: 2026-11-30
weight: 12
chapter: false
pre: " <b> 1.12 </b> "
---

### OBJECTIVES IN WEEK 12:
- Keep CPAWS Production running stably until grading is complete; monitor the alarms and the daily accuracy job.
- Finalize the term's AWS cost with the team and summarize the project lessons learned.
- Master cost and compliance governance through advanced IAM policies (limit by Region, Instance Type).
- Set up end-to-end monitoring: from the network layer (VPC Flow Logs) and the application layer (Grafana) to the AWS service layer (CloudWatch).

### TASKS OF WEEK 12:

| Day | Task | Start Date | End Date | References |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Practice Lab 30: Cost and resource usage governance with IAM on AWS**<br>+ Preparation<br>* Create an IAM Group<br>* Create an IAM user<br>+ Limit by Region<br>* Create the restriction policy<br>* Attach the policy to the group<br>* Test the policy<br>+ Limit by EC2 family<br>* Create the restriction policy<br>* Attach the policy to the group<br>* Test the policy<br>+ Limit by instance size<br>* Update the restriction policy<br>* Test the policy<br>+ Limit by EBS volume<br>* Create the restriction policy<br>* Attach the policy to the group<br>* Test the policy<br>+ Clean up resources | 30/11/2026 | 30/11/2026 | [Lab 30 Intro](https://000064.awsfcaj.com/vi/1-preparation/)<br>[Limit Region](https://000064.awsfcaj.com/vi/2-limit-region/)<br>[Limit Family](https://000064.awsfcaj.com/vi/3-limit-family/)<br>[Limit Size](https://000064.awsfcaj.com/vi/4-limit-size/) |
| **Tue** | **- Practice Lab 31: Monitor network infrastructure with VPC Flow Logs**<br>+ Introduction<br>+ Preparation<br>+ Enable VPC Flow Logs<br>+ Monitor the network infrastructure<br>+ Clean up resources | 01/12/2026 | 01/12/2026 | [Lab 31 Intro](https://000074.awsfcaj.com/vi/1-introduce/)<br>[VPC Flow Logs](https://000074.awsfcaj.com/vi/4-enablevpcflowlogs/) |
| **Wed** | **- Practice Lab 32: Getting started with Grafana on AWS**<br>+ Introduction<br>+ Preparation<br>* Create a VPC and subnet<br>* Create a Security Group<br>* Create an EC2 Instance<br>* Create an IAM User<br>* Create an IAM Role<br>* Attach the IAM Role<br>+ Install Grafana<br>+ Monitor with Grafana<br>+ Clean up resources | 02/12/2026 | 02/12/2026 | [Lab 32 Intro](https://000029.awsfcaj.com/vi/1-introduce/)<br>[Install Grafana](https://000029.awsfcaj.com/vi/3-installgrafana/) |
| **Thu** | **- (Planned) CPAWS project: wrap-up**<br>+ Check that Production and the alarms are still working<br>+ Update the workshop page with the final term cost | 03/12/2026 | 03/12/2026 | [AWS Cost Explorer](https://aws.amazon.com/aws-cost-management/aws-cost-explorer/) |
| **Fri** | **- Practice Lab 33: AWS CloudWatch Workshop**<br>+ Introduction<br>+ Preparation<br>+ CloudWatch Metrics<br>* Viewing Metrics<br>* Search expressions<br>* Math expressions<br>* Dynamic Labels<br>+ CloudWatch Logs<br>* CloudWatch Logs<br>* CloudWatch Logs Insights<br>* CloudWatch Metric Filter<br>+ CloudWatch Alarms<br>+ CloudWatch Dashboards<br>+ Clean up resources | 04/12/2026 | 04/12/2026 | [Lab 33 Intro](https://000036.awsfcaj.com/vi/1-introduce/)<br>[Metrics](https://000036.awsfcaj.com/vi/3-cloudwatchmetric/)<br>[Logs](https://000036.awsfcaj.com/vi/4-cloudwatchlogs/)<br>[Alarms](https://000036.awsfcaj.com/vi/5-cloudwatchalarm/) |

### ACHIEVEMENTS IN WEEK 12:
1. (Planned) CPAWS Production runs continuously until grading is complete, with the final term cost figure.
2. (Planned) Summarize the project lessons learned and update the workshop page.
3. Set strict IAM policies to control resource creation, prevent budget overruns (cost control) and ensure security.
4. Built multi-layer monitoring: network traffic with VPC Flow Logs and application metrics visualized on Grafana/CloudWatch Dashboards.
