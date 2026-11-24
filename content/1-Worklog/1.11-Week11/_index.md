---
title: "Week 11 Worklog"
date: 2026-11-23
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### OBJECTIVES IN WEEK 11:
- Freeze CPAWS project features (22/11), record the demo video and complete the submission package (repo README, workshop page).
- Master the development of complex Serverless applications (front-end, backend, image processing) with AWS SAM.
- Practice a professional Machine Learning workflow on Amazon SageMaker: from feature engineering to model deployment.
- Boost coding productivity with the AWS AI tools (Amazon Q, CodeWhisperer) in VS Code.

### TASKS OF WEEK 11:

| Day | Task | Start Date | End Date | References |
|:---:|---|:---:|:---:|---|
| **Mon–Wed** | **- CPAWS project: release v1.0.1 and complete the submission package**<br>+ 23/11: merged PR #146 (the chatbot knows the current date and time) and #145 (web v1.0.1: mobile, reading guide, session chat history), release #149, tag `v1.0.1`; deployed `CpawsApi` and Amplify Deployment 7<br>+ 24/11: revised ADR-0014 after the Data & Security Lead review (#148, self sign-up stays open for a limited time, until 10/12 at the latest); recorded chatbot limitations found on Production (#150); cleaned up repository docs (#151); took Console and web screenshots; the team finalized the cost estimate of 5.26 USD/month<br>+ 25/11: updated the workshop pages (vi/en) for v1.0.1 with screenshots; drafted the team profile and the `.xml` architecture file | 23/11/2026 | 25/11/2026 | [CPAWS repo](https://github.com/CPAWS-AWS-FCAJ/CPAWS-commodity-forecasting)<br>[Release v1.0.1](https://github.com/CPAWS-AWS-FCAJ/CPAWS-commodity-forecasting/releases/tag/v1.0.1) |
| **Mon** | **- Practice Lab 26: Serverless - Writing a front-end that calls API Gateway**<br>+ Deploy the front-end<br>+ Set up API Gateway<br>+ Test the API with Postman<br>+ Test the API with the front-end<br>+ Clean up resources | 23/11/2026 | 23/11/2026 | [Lab 26 Intro](https://000135.awsfcaj.com/vi/1-front-end-deployment/)<br>[API Gateway](https://000135.awsfcaj.com/vi/2-config-api-gw/) |
| **Tue** | **- Practice Lab 27: Serverless with Lambda, API Gateway and SAM**<br>+ Introduction<br>+ Preparation<br>* Create a Cloud9 instance<br>* Create a CodeCommit repository<br>+ Deploy the sample application<br>* Deploy the front-end<br>* Deploy the backend<br>Deploy the infrastructure<br>Fill in ride information<br>Check the configuration<br>* Update the front-end<br>+ Deploy the ride times system<br>* Deploy the backend<br>* Deploy the front-end<br>+ On-ride photo processing<br>* Deploy the backend<br>Create the Chroma Key Lambda function<br>Create the Photo-Compositing Lambda function<br>Post-processing<br>* Test the feature<br>+ Clean up resources | 24/11/2026 | 24/11/2026 | [Lab 27 Intro](https://000066.awsfcaj.com/vi/1-introduce/)<br>[Deploy App](https://000066.awsfcaj.com/vi/3-deployapp/)<br>[Image Processing](https://000066.awsfcaj.com/vi/5-on-ridesphotoprocessing/) |
| **Wed** | **- Practice Lab 28: SageMaker Immersion Day starter workshop**<br>+ Preparation<br>* Create SageMaker Studio<br>* Download the GitHub repo<br>+ Feature Engineering<br>* Prepare the dataset<br>* Set up Data Wrangler<br>* Analyze the dataset<br>* Correlation analysis<br>* Transform the data<br>* Feature Store<br>* Export data to S3<br>+ Train/Tune/Deploy XGBoost<br>* Train & tune the model<br>* Deploy the model<br>* Evaluate model performance<br>* Automatic model tuning<br>+ Clean up resources | 25/11/2026 | 25/11/2026 | [Lab 28 Prepare](https://000200.awsfcaj.com/vi/1-prepare/)<br>[Feature Engineering](https://000200.awsfcaj.com/vi/2-feature-engineering/)<br>[Train Deploy](https://000200.awsfcaj.com/vi/3-train-tune-deploy/) |
| **Fri** | **- Practice Lab 29: AWS Toolkit for VS Code: Amazon Q & CodeWhisperer**<br>+ Introduction<br>+ AWS Toolkit for Visual Studio Code<br>+ Connect the AWS account<br>+ Change the AWS Region<br>+ Authentication<br>* IAM Identity Center<br>* AWS IAM credentials<br>* Create an AWS Builder ID<br>+ Work with services<br>* AWS Explorer<br>* Amazon Q<br>* Amazon CodeWhisperer | 27/11/2026 | 27/11/2026 | [Lab 29 Intro](https://000087.awsfcaj.com/vi/1-introduce/)<br>[Install Toolkit](https://000087.awsfcaj.com/vi/2-install/)<br>[Authentication](https://000087.awsfcaj.com/vi/5-authentication-and-access-for-the-aws-toolkit-for-visual-studio-code/)<br>[Amazon Q](https://000087.awsfcaj.com/vi/6-working-with-aws-services/) |

### ACHIEVEMENTS IN WEEK 11:
1. The CPAWS project froze features on schedule on 22/11; released v1.0.0 (22/11) and v1.0.1 (23/11) to Production, revised ADR-0014 on Cognito self sign-up, recorded the chatbot limitations and produced a cost estimate.
2. Deployed a complete multi-function Serverless application (including real-time image processing) with AWS SAM (Serverless Application Model).
3. Completed a Machine Learning pipeline on SageMaker: from data preparation (Data Wrangler) to training and deploying an XGBoost model.
4. Configured an AI-assisted coding environment (Amazon Q, CodeWhisperer) in VS Code to speed up coding and debugging.
5. The personal workshop page is updated for v1.0.1, in Vietnamese and English, with Console and web screenshots.
