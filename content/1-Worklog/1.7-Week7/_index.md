---
title: "Week 7 Worklog"
date: 2026-10-26
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### OBJECTIVES IN WEEK 7:
- Finalize and optimize the CPAWS project architecture against the Well-Architected criteria.
- Master in-memory caching with Amazon ElastiCache (Redis): create clusters, connect via CLI/SDK and work with data.
- Complete the CPAWS project proposal and push it to GitHub.
- Practice advanced networking: Transit Gateway, Site-to-Site VPN (with Cisco CSR), ECMP routing and Hybrid DNS with Route 53.
- Join a charity activity making STEM stuffed animals with the AWS HCM team.

### TASKS OF WEEK 7:

| Day | Task | Start Date | End Date | References |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Attend volunteer event: making STEM stuffed animals with AWS HCM**<br> **- Team meeting: review the Well-Architected criteria and revise the project architecture**<br>+ Batch forecasting with a Lambda container (NumPy) instead of an always-on SageMaker endpoint<br>+ Agreed the target with Long: h-session log return (h = 1–7), 7 quantiles, pinball loss | 26/10/2026 | 26/10/2026 | [AWS Well-Architected](https://aws.amazon.com/architecture/well-architected/) |
| **Tue** | **- Practice Lab 16: Amazon ElastiCache - Redis**<br>+ Introduction<br>+ Preparation<br>* Create an AWS Access Key<br>* Install the AWS CLI<br>* Configure the AWS CLI<br>+ Create an ElastiCache cluster<br>* Create a subnet group (Console/AWS CLI)<br>* Create a cluster (cluster mode disabled) (Console/AWS CLI)<br>* Create a cluster (cluster mode enabled)<br>* Grant access to the cluster<br>* Connect to a cluster node<br>Cluster Mode Disabled<br>Cluster Mode Enabled<br>Endpoints using the AWS CLI<br>* Delete the cluster (Console/AWS CLI) | 27/10/2026 | 27/10/2026 | [Lab 16 Intro](https://000061.awsfcaj.com/vi/1-introduce/)<br>[Prereq](https://000061.awsfcaj.com/vi/2-prerequiste/)<br>[Redis Lab](https://000061.awsfcaj.com/vi/3-amazonelasticacheforredis/) |
| **Wed** | **- Continue Lab 16: Amazon ElastiCache - Redis**<br>+ Use the AWS SDK to write and read data on ElastiCache<br>* Create an ElastiCache cluster<br>Create a cluster (cluster mode disabled)<br>Create a cluster (cluster mode enabled)<br>Check the user group<br>* Connect to ElastiCache<br>Connect to a cluster (cluster mode disabled)<br>Connect to a cluster (cluster mode enabled)<br>* Set and Get strings<br>* Set and Get a hash with multiple items<br>* Publish (write) and subscribe (read)<br>* Write and read from a stream<br>+ Clean up resources | 28/10/2026 | 28/10/2026 | [SDK Redis](https://000061.awsfcaj.com/vi/4-sdkelasticache/)<br>[Cleanup](https://000061.awsfcaj.com/vi/5.cleanupresource/) |
| **Thu** | **- Complete the whole CPAWS proposal**<br>+ Wrote the API document: the `Forecasts` table and the `/health`, `/forecasts`, `/forecasts/{asset}` endpoints | 29/10/2026 | 29/10/2026 | [API Gateway REST API](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-rest-api.html) |
| **Fri** | **- Practice Lab 17: AWS Networking and Content Delivery**<br>+ Introduction<br>+ Preparation<br>* Deploy the VPCs<br>* Cisco CSR Agreement<br>+ VPC Components Deep Dive<br>+ Transit Gateway and Site-to-Site VPNs<br>* Transit Gateway and Site-to-Site VPNs<br>* Access the Cisco CSR Router with Cloud9<br>* Set up the Site-to-Site VPN<br>* Add an ECMP path<br>* Transit Gateway routing<br>+ Route 53 DNS Endpoints and Internal Hosted Zones<br>* Deploy DNS<br>* Test DNS | 30/10/2026 | 30/10/2026 | [Lab 17 Intro](http://000092.awsfcaj.com/vi/1-introduce/)<br>[VPC Deep Dive](https://000092.awsfcaj.com/vi/3-vpcs/)<br>[TGW & VPN](https://000092.awsfcaj.com/vi/4-transitgatewayandvpn/)<br>[Route53](https://000092.awsfcaj.com/vi/5-route53/) |

### ACHIEVEMENTS IN WEEK 7:
1. The CPAWS project architecture was finalized.
2. Completed Lab 16: learned Amazon ElastiCache (Redis), created clusters, connected and worked with advanced data features (Pub/Sub, Stream) via the AWS SDK.
3. Completed the project proposal and API documentation and pushed them to the GitHub repository.
4. Completed Lab 17: deployed a complex network using Transit Gateway with VPN to a Cisco CSR router, configured ECMP routing and Hybrid name resolution with Route 53.
5. Contributed to the charity activity making STEM stuffed animals for children with the AWS HCM team.
