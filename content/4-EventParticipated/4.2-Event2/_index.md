---
title: "Event 2"
date: 2026-09-29
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Summary Report: "AWS Cloud and AI Day Hanoi – Watch Party: Ho Chi Minh City"

### Event Overview

- **Event Name:** AWS Cloud and AI Day Hanoi – Watch Party: Ho Chi Minh City  
- **Date:** September 29, 2026  
- **Role:** Attendee  
- **Format:** Watch party: following the Hanoi event live from the Ho Chi Minh City venue  
- **Location:** Floor 26, Bitexco Financial Tower, District 1, Ho Chi Minh City  
- **Agenda:** [Event agenda](https://aws.amazon.com/events/cloud-day-hanoi/agenda/)

AWS Cloud & AI Day is AWS's annual event in Vietnam, bringing together AWS experts, partners and customers to share the latest in cloud and AI. In 2026 the headline topic was agentic AI: how to take AI agents from demo to real operation, and the data foundation needed for AI to be reliable. Together with other FCAJ interns, I followed the event at floor 26 of the AWS office in Ho Chi Minh City.

### Program Highlights

#### Opening Keynote
The keynote centered on one question: what does it take to solve problems everyone says are impossible? My key notes:
- Innovation starts from customer problems, not from technology
- Generative AI and agentic AI are moving from experimentation to real business value
- Global infrastructure and managed services let small teams build big products
- Vietnamese companies are accelerating their digital transformation on AWS

#### Agentic AI: From Demo to Deployment
- **The demo-to-production gap:** an agent that works in a demo may not be reliable on real data; it needs testing, monitoring and limited permissions
- **Agents for software development:** AI can write code, tests, reviews and documentation, but humans remain accountable for quality
- **Migration at scale with AI:** how AWS Transform uses agents to help modernize and migrate legacy applications

#### AI-Ready Data Foundations
- Scattered, non-standardized data is the biggest barrier when bringing AI into products
- A data lake on Amazon S3 as the foundation, combined with data governance (catalog, access control, data quality)
- A model is only as good as its input data: data lineage and processing must be traceable

#### Application Modernization and Platform Engineering
- A Vietnamese bank's story of building an Internal Developer Platform on Amazon EKS
- Choosing the right tool for the job: complex architectures are not always needed; a simpler solution can be more effective and cheaper
- An introduction to Amazon Quick with an AI-assistant building challenge for attendees

### Key Technical Takeaways

- **Agents need guardrails:** least-privilege IAM, a limited tool scope, and logging every action for review
- **Evaluate before trusting:** a fixed test question set is needed to measure agent quality, not just a few manual tries
- **Layered data lake:** separate raw, cleaned and feature data, each layer with clear rules
- **Serverless and managed services:** let small teams focus on the product instead of running servers

### Personal Learnings

#### Technical Growth
- I understood better that the hard part of real-world AI lies in data, testing and operations, not just the model
- The part on evaluating agents with a fixed question set helped me picture how our team could test the project chatbot

#### Industry Insights
- Vietnamese companies, especially in banking and finance, are investing heavily in data platforms and AI
- Demand is growing for engineers who understand data, models and cloud together

#### Application to the CPAWS Project
- Designing the S3 data lake with `raw/`, `clean/`, `features/`, `models/`, `eval/` layers so every number in the report is traceable
- The chatbot takes numbers only from tools and never makes up figures — the "agent with guardrails" idea repeated throughout the event
- Preparing a fixed test question set for the chatbot and running the official scoring only once

### Event Experience

This was the first big AWS event I attended since starting my internship. The atmosphere was lively, and hearing real stories first-hand showed me how valuable the Lab skills are once applied in companies. I mostly listened, took notes and followed the presentations; many of those notes later went into our team discussions when designing the CPAWS architecture.

The event also made me more confident in our direction: a useful AI product does not need the most complex model, but trustworthy data, honest evaluation and a stable system.

### Event Photos

![AWS Cloud & AI Day 2026](/images/4-Event/cloudday.jpg)
