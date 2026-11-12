---
title: "Week 7 Worklog"
date: 2026-10-18
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### OBJECTIVES IN WEEK 7:
- Finalize and optimize the CPAWS architecture based on Mentor feedback.
- Master In-memory Caching with Amazon ElastiCache (Redis): Create clusters, connect via CLI/SDK, and manipulate data.
- Complete and submit the CPAWS project Proposal to GitHub.
- Practice Advanced Networking: Transit Gateway, Site-to-Site VPN (with Cisco CSR), ECMP Routing, and Hybrid DNS with Route 53.

### TASKS OF WEEK 7:

| Day | Task | Start Date | End Date | References |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Receive feedback on the project solution architecture from FCAJ mentors**<br>**- Listen and refine the architecture based on feedback**<br>+ Batch prediction with a Lambda container (NumPy) instead of an always-on SageMaker endpoint<br>+ Agreed with Long on the target: h-session log return (h = 1–7), 7 quantiles, pinball loss; recorded as an ADR | 19/10/2026 | 19/10/2026 | [AWS Well-Architected](https://aws.amazon.com/architecture/well-architected/)<br>[ADR](https://adr.github.io/)<br>[Proper Scoring Rules](https://doi.org/10.1198/016214506000001437) |
| **Tue** | **- Practice Lab 16: Amazon ElastiCache - Redis**<br>+ Introduction<br>+ Preparation<br>* Create AWS Access Key<br>* Install AWS CLI<br>* Configure AWS CLI<br>+ Create ElastiCache cluster<br>* Create subnet group (Console/AWS CLI)<br>* Create cluster (cluster mode disabled) (Console/AWS CLI)<br>* Create cluster (cluster mode enabled)<br>* Grant access to cluster<br>* Connect to cluster node<br>Cluster Mode Disabled<br>Cluster Mode Enabled<br>Endpoints use AWS CLI<br>* Delete cluster (Console/AWS CLI) | 20/10/2026 | 21/10/2026 | [Lab 16 Intro](https://000061.awsstudygroup.com/vi/1-introduce/)<br>[Prereq](https://000061.awsstudygroup.com/vi/2-prerequiste/)<br>[Redis Lab](https://000061.awsstudygroup.com/vi/3-amazonelasticacheforredis/) |
| **Wed** | **- Continue Lab 16: Amazon ElastiCache - Redis**<br>+ Use AWS SDK to read/write data on ElastiCache<br>* Create Elasticache cluster<br>Create a cluster (mode disabled cluster)<br>Create a cluster (mode enabled cluster)<br>Check user group<br>* Connect to Elasticache<br>Connect to a cluster (mode disabled cluster)<br>Connect to a cluster (mode enabled cluster)<br>* Set and Get strings<br>* Set and Get a hash with multiple items<br>* Publish (write) and subscribe (read)<br>* Write and read from a stream<br>+ Resource Cleanup | 21/10/2026 | 21/10/2026 | [SDK Redis](https://000061.awsstudygroup.com/vi/4-sdkelasticache/)<br>[Cleanup](https://000061.awsstudygroup.com/vi/5.cleanupresource/) |
| **Thu** | **- Finalize the whole CPAWS proposal, deploy to GitHub, and submit the proposal**<br>+ Wrote the API document (`contract.md`): the `Forecasts` table and the `/health`, `/forecasts`, `/forecasts/{asset}` endpoints | 22/10/2026 | 22/10/2026 | [Proposal Guide](https://workshop-sample.fcjuni.com/2-proposal/)<br>[API Gateway REST API](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-rest-api.html) |
| **Fri** | **- Practice Lab 17: AWS Networking and Content Delivery**<br>+ Introduction<br>+ Preparation Steps<br>* Deploy VPC<br>* Cisco CSR Agreement<br>+ VPC Components Deep Dive<br>+ Transit Gateway and Site-to-Site VPNs<br>* Transit Gateway and Site-to-Site VPNs<br>* Access Cisco CSR Router with Cloud9<br>* Setup Site-to-site VPN<br>* Add ECMP Path<br>* Transit Gateway Routing<br>+ Route53 DNS Endpoints and Internal Hosted Zones<br>* Deploy DNS<br>* Test DNS | 23/10/2026 | 23/10/2026 | [Lab 17 Intro](http://000092.awsstudygroup.com/vi/1-introduce/)<br>[VPC Deep Dive](https://000092.awsstudygroup.com/vi/3-vpcs/)<br>[TGW & VPN](https://000092.awsstudygroup.com/vi/4-transitgatewayandvpn/)<br>[Route53](https://000092.awsstudygroup.com/vi/5-route53/) |

### ACHIEVEMENTS IN WEEK 7:
1. The CPAWS architecture was consolidated after Mentor feedback: batch prediction, no 24/7 resources, fitting the credit budget.
2. Completed Lab 16: Mastered Amazon ElastiCache (Redis), practiced creating Clusters, connecting, and advanced data operations (Pub/Sub, Stream) via AWS SDK.
3. Completed the project Proposal and API document, successfully submitted to the GitHub Repository.
4. Completed Lab 17: Deployed a complex network model using Transit Gateway with VPN to a Cisco CSR Router, configured ECMP routing, and Hybrid DNS resolution with Route 53.
