---
title: "Event 3"
date: 2026-10-02
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

# Summary Report: "Fireside Chat with Dr. Werner"

### Event Overview

- **Event Name:** Fireside Chat with Dr. Werner (AWS CTO), with the AWS FCAJ HCM community  
- **Date:** October 2, 2026  
- **Role:** Attendee  
- **Format:** In-person talk and conversation  
- **Location:** Floor 26, Bitexco Financial Tower, District 1, Ho Chi Minh City  

The session was organized by the AWS FCAJ HCM community and announced on the program's channels. It was a rare chance for interns to hear a senior AWS technology leader speak first-hand about how Amazon builds systems, its engineering culture, and what young engineers should prepare for in the AI era. The room was full, with interns, mentors and community members attending.

### Key Topics

#### Amazon's Builder Culture
- Everything starts from the customer: write down the problem and the benefit for users before choosing technology
- Small teams fully own their services: "you build it, you run it"
- Experiment boldly, but measure results and fix mistakes quickly

#### Designing Simple, Resilient Systems
- Complex systems that work usually evolve from simple systems that worked; avoid designing for complexity from day one
- Design for failure: any component can break, so decouple parts so one failure does not bring the whole system down
- Observability is part of the design, not an afterthought

#### Cost as an Architectural Requirement
- Cost should be considered at design time, just like security or performance
- Resources running all the time without serving anyone are waste; prefer on-demand or scheduled architectures
- Measure cost per unit of value (for example cost per forecast) to know whether a system is sustainable

#### AI and the Software Engineering Profession
- AI makes engineers faster, but engineers remain responsible for correctness, security and quality
- Core skills matter more than ever: understanding the problem, asking the right questions, verifying results
- Advice for students: build strong fundamentals, stay curious, build real products rather than only studying theory

### Key Takeaways

- **Start from users:** a product is more useful when it answers what users really need
- **Simple first, optimize later:** pick a good-enough solution you can measure, then improve
- **Design for failure and for cost:** both belong in the very first design
- **A builder's responsibility:** use AI to speed up, but always verify

### Personal Learnings

#### A Change in Mindset
- Before this talk, I thought an AI project should use the most complex model possible. Afterwards, I understood that a simple model evaluated honestly is worth more than a complex model that cannot prove its value
- I learned to see cost as a design criterion rather than a number checked after the system is built

#### Application to the CPAWS Project
- The team chose a scheduled batch-prediction architecture (EventBridge Scheduler → Lambda) with no 24/7 endpoints, keeping costs within the credit budget
- A machine learning model replaces the running model only when it is proven better than the statistical baseline — the "measure results" principle
- Components (ingestion, prediction, chatbot) are decoupled so one failing data source does not stop the whole system

### Event Experience

The session had a friendly, close atmosphere. I listened, took notes and followed the exchange between the speaker and the audience. What impressed me most was how the speaker described very simple principles applied consistently at enormous scale, showing that good engineering is about discipline, not complexity.

The talk came right when the CPAWS team was about to finalize its architecture, so I brought many of these ideas into our discussions the following week.

### Event Photos

![Fireside Chat with Dr. Werner](/images/4-Event/CTO.jpg)
