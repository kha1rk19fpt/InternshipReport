---
title: "Infrastructure with CDK"
date: 2026-11-20
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---
#### Overview

All infrastructure is written in **AWS CDK (Python)** under `infra/`, split into small stacks that can be deployed and reviewed independently.

1. [Bootstrap and data stack](5.4.1-Data-stack/)
2. [Daily flow](5.4.2-Daily-flow/)
3. [API and authentication](5.4.3-API-Cognito/)

#### Cost and Security Principles

- No 24/7 endpoints, NAT Gateway or OpenSearch Serverless
- Lambdas calling external APIs run **outside a VPC**
- Each Lambda has a least-privilege IAM role
- Estimate cost before adding any new resource
