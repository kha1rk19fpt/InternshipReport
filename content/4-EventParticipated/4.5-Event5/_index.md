---
title: "Event 5"
date: 2026-11-14
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---

# Summary Report: "Cloud Mastery 1"

### Event Overview

- **Event Name:** Cloud Mastery 1  
- **Date:** November 14, 2026  
- **Role:** Attendee  
- **Format:** Technical talk  
- **Location:** Floor 26, Bitexco Financial Tower, District 1, Ho Chi Minh City  
- **Main Topic:** Amazon Bedrock AgentCore

Cloud Mastery 1 was a technical session whose main topic was Amazon Bedrock AgentCore. The opening part, "AI in the Enterprise", placed AI inside a product rather than treating it as the product: data are the ingredients, the AI model is built on them, a feature is built on the model, and users meet the AI only through that feature.

### Amazon Bedrock AgentCore in Brief

*Summarized from public AWS material.*

- Amazon Bedrock provides foundation models; AgentCore is a set of managed services for running agents built on those models in production
- Main components include **Runtime** (where the agent code runs), **Gateway** (turns APIs and Lambda functions into tools for the agent), **Memory** (short-term and long-term context), **Identity** (authentication for agents and their users), **Observability**, plus built-in tools such as Code Interpreter and Browser
- The components can be adopted one at a time; a team does not have to use the whole set

### Connection to the CPAWS Project

- The event came about a week before the CPAWS feature freeze (22/11), when the team's chatbot was already running
- The CPAWS chatbot is a small, hand-built version of the same pieces: the `chat-api` Lambda function plays the role of the runtime, two Python tools (`get_forecast`, `get_news`) are called through the Bedrock Converse API, users sign in with Amazon Cognito, and daily usage limits are stored in DynamoDB. There is no long-term conversation memory
- If the chatbot grows (more tools, memory across sessions), AgentCore Gateway, Memory and Identity are the parts worth evaluating instead of building more infrastructure ourselves

### Event Photos

![Cloud Mastery 1](/images/4-Event/cloud_mastery_1.jpg)

### References

- [Amazon Bedrock AgentCore](https://aws.amazon.com/bedrock/agentcore/)
- [What is Amazon Bedrock AgentCore? (Developer Guide)](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html)
