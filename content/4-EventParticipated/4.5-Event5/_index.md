---
title: "Event 5"
date: 2026-11-14
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---

# Event: "Cloud Mastery"

### Event Overview

- **Event Name:** Cloud Mastery  
- **Date:** 14/11/2026  
- **Role:** Attendee  
- **Format:** Technical talk  
- **Location:** 26th floor, Bitexco Financial Tower, District 1, Ho Chi Minh City  
- **Main Topic:** Taking AI from prototype to production, Amazon Bedrock AgentCore

### Activities

#### From Prototype to Production

- The speaker compared AI in academia with AI in the enterprise using the image of a home kitchen and a restaurant chain. Enterprise AI is judged by business value, not only by accuracy; it has to work with constantly changing data and serve a very large number of users 24/7.
- The model is only one part of a production system. Alongside the model, you also need data pipelines, monitoring, security, scalability, reliability and cost control.

#### Four Pillars on AWS

- **Security:** AWS KMS protects data, AWS IAM controls access, and guardrails handle AI-specific risks, for example users trying to make the model give wrong answers.
- **Scalability:** AWS Lambda, Auto Scaling and Elastic Load Balancing handle changing traffic.
- **Reliability:** health checks, retries, failover and alerts keep the system running when a component fails.
- **Cost optimization:** pay for what you use, choose the right configuration, use caching. For AI, a large model orchestrates while smaller, cheaper models handle simple tasks; repeated questions are answered from the cache.

#### Amazon Bedrock AgentCore

- The Duolingo case study showed the benefits of a shared agent platform, instead of each team rebuilding the same infrastructure.
- A demo of an order-lookup agent written with the Strands Agents SDK, running on AgentCore Runtime (managed compute, isolated sessions, automatic scaling). Even with a managed runtime, the development team is still responsible for business rules, data, tools and testing.
- **Identity** separates inbound authentication (who the user is and whether they may call the agent) from outbound authentication (what the agent may access on the user's behalf).
- **Gateway** gives the agent a single, secure access point to many tools, APIs and MCP servers.
- **Memory** includes short-term memory for the current conversation and long-term memory for user preferences and summaries.
- **Observability** integrates with Amazon CloudWatch and tracks each step of the agent: model calls, tool calls, latency, token counts.

#### Q&A and Closing

- Attendees asked the speaker questions, took a mini quiz, filled in a feedback form and took souvenir photos.

### What I Learned

#### Production-Ready AI

- A working model is only the starting point; security, scalability, reliability and cost must be designed in from the beginning.

#### Agent Architecture

- Understanding how an agent framework such as Strands works with a managed platform such as AgentCore, and why identity, tool access and memory should use shared components instead of being rebuilt in each project.

#### Agent Security and Monitoring

- Separate inbound and outbound permissions, and give each agent only the permissions it needs.
- Agents often give wrong answers without reporting an error, so every run needs to be logged, monitored and measured against goals set in advance.

### Event Photos

![Cloud Mastery](/images/4-Event/cloud_mastery_1.jpg)
