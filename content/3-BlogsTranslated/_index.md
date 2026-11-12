---
title: "Technical Blogs"
date: 2026-09-08
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

The three blog posts below are written by me, summarizing what I learned and researched while building the model and infrastructure of the CPAWS project.

### [Blog 1 - Don't Be Fooled by "99% Accuracy": Forecasting Return Distributions with Pinball Loss](3.1-Blog1/)

Why forecasting price levels creates illusory accuracy, how to switch to forecasting 7 quantiles of log returns, scoring with pinball loss, and comparing against a 252-session quantile baseline. It ends with a real result: DLinear lost to the statistical baseline in 28/28 groups on the 2019 validation set, and the lessons learned.

### [Blog 2 - Five Look-Ahead Leakage Traps in Financial Time-Series Forecasting](3.2-Blog2/)

Five ways future information leaks into a model: full-series CEEMDAN, wrong scaling, dating news by event instead of publication, forward-filling the target across exchange holidays, and reusing the test set. Each trap comes with a fix and a sample automated test.

### [Blog 3 - Serverless Batch Prediction on AWS: EventBridge Scheduler, Lambda Container Images and DynamoDB](3.3-Blog3/)

How to build a daily prediction flow with no 24/7 resources: scheduling with EventBridge Scheduler, chaining Lambdas with Lambda Destinations, NumPy inference in a container image, query-driven DynamoDB design, and a cost estimate.
