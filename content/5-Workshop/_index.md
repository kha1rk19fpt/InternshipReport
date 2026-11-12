---
title: "Workshop"
date: 2026-11-20
weight: 5
chapter: false
pre: " <b> 5. </b> "
---
# CPAWS - Building a Serverless Commodity Price Forecasting Platform on AWS

#### Overview

**CPAWS** is a web application that forecasts gold, silver, Brent and WTI prices 1–7 days ahead, shown in USD and VND, with a Vietnamese chatbot on Amazon Bedrock that explains the forecasts. The whole system runs serverless on a schedule, with no 24/7 resources.

In this workshop, you will learn how to:
- Collect price data by trading calendar and store it in an Amazon S3 data lake
- Build a quantile forecasting baseline without look-ahead leakage
- Deploy infrastructure with AWS CDK: S3, ECR, Lambda container images, DynamoDB
- Automate the daily flow with EventBridge Scheduler and Lambda Destinations
- Expose an authenticated API with API Gateway + Cognito
- Call the Amazon Bedrock Converse API with tool use so the chatbot only uses system data

#### Contents

1. [Introduction](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [Data and model](5.3-Data-and-model/)
4. [Infrastructure with CDK](5.4-Infrastructure/)
5. [Chatbot with Amazon Bedrock](5.5-Chatbot/)
6. [Cost estimation and optimization](5.6-Cost/)
7. [Cleanup](5.7-Cleanup/)
