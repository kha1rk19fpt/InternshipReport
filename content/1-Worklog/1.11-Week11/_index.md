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
- Learn more about cost and compliance governance through advanced IAM policies (limit by Region, Instance Type).

### TASKS OF WEEK 11:

| Day | Task | Start Date | End Date | References |
|:---:|---|:---:|:---:|---|
| **Mon** | **- CPAWS project: production release, completing the submission package and recording the demo video**<br>**- Practice Lab 26: Serverless - Writing a front-end that calls API Gateway**<br>+ Deploy the front-end<br>+ Set up API Gateway<br>+ Test the API with Postman<br>+ Test the API with the front-end<br>+ Clean up resources | 23/11/2026 | 25/11/2026 | [Lab 26 Intro](https://000135.awsfcaj.com/vi/1-front-end-deployment/)<br>[API Gateway](https://000135.awsfcaj.com/vi/2-config-api-gw/) |
| **Tue** | **- Practice Lab 27: Serverless with Lambda, API Gateway and SAM**<br>+ Introduction<br>+ Preparation<br>* Create a Cloud9 instance<br>* Create a CodeCommit repository<br>+ Deploy the sample application<br>* Deploy the front-end<br>* Deploy the backend<br>Deploy the infrastructure<br>Fill in ride information<br>Check the configuration<br>* Update the front-end<br>+ Deploy the ride times system<br>* Deploy the backend<br>* Deploy the front-end<br>+ On-ride photo processing<br>* Deploy the backend<br>Create the Chroma Key Lambda function<br>Create the Photo-Compositing Lambda function<br>Post-processing<br>* Test the feature<br>+ Clean up resources | 24/11/2026 | 24/11/2026 | [Lab 27 Intro](https://000066.awsfcaj.com/vi/1-introduce/)<br>[Deploy App](https://000066.awsfcaj.com/vi/3-deployapp/)<br>[Image Processing](https://000066.awsfcaj.com/vi/5-on-ridesphotoprocessing/) |
| **Wed** | **- Practice Lab 28: SageMaker Immersion Day starter workshop**<br>+ Preparation<br>* Create SageMaker Studio<br>* Download the GitHub repo<br>+ Feature Engineering<br>* Prepare the dataset<br>* Set up Data Wrangler<br>* Analyze the dataset<br>* Correlation analysis<br>* Transform the data<br>* Feature Store<br>* Export data to S3<br>+ Train/Tune/Deploy XGBoost<br>* Train & tune the model<br>* Deploy the model<br>* Evaluate model performance<br>* Automatic model tuning<br>+ Clean up resources | 25/11/2026 | 25/11/2026 | [Lab 28 Prepare](https://000200.awsfcaj.com/vi/1-prepare/)<br>[Feature Engineering](https://000200.awsfcaj.com/vi/2-feature-engineering/)<br>[Train Deploy](https://000200.awsfcaj.com/vi/3-train-tune-deploy/) |
| **Thu** | **- Practice Lab 29: AWS Toolkit for VS Code: Amazon Q & CodeWhisperer**<br>+ Introduction<br>+ AWS Toolkit for Visual Studio Code<br>+ Connect the AWS account<br>+ Change the AWS Region<br>+ Authentication<br>* IAM Identity Center<br>* AWS IAM credentials<br>* Create an AWS Builder ID<br>+ Work with services<br>* AWS Explorer<br>* Amazon Q<br>* Amazon CodeWhisperer | 26/11/2026 | 26/11/2026 | [Lab 29 Intro](https://000087.awsfcaj.com/vi/1-introduce/)<br>[Install Toolkit](https://000087.awsfcaj.com/vi/2-install/)<br>[Authentication](https://000087.awsfcaj.com/vi/5-authentication-and-access-for-the-aws-toolkit-for-visual-studio-code/)<br>[Amazon Q](https://000087.awsfcaj.com/vi/6-working-with-aws-services/) |
| **Fri** | **- Practice Lab 30: Cost and resource usage governance with IAM on AWS**<br>+ Preparation<br>* Create an IAM Group<br>* Create an IAM user<br>+ Limit by Region<br>* Create the restriction policy<br>* Attach the policy to the group<br>* Test the policy<br>+ Limit by EC2 family<br>* Create the restriction policy<br>* Attach the policy to the group<br>* Test the policy<br>+ Limit by instance size<br>* Update the restriction policy<br>* Test the policy<br>+ Limit by EBS volume<br>* Create the restriction policy<br>* Attach the policy to the group<br>* Test the policy<br>+ Clean up resources | 27/11/2026 | 27/11/2026 | [Lab 30 Intro](https://000064.awsfcaj.com/vi/1-preparation/)<br>[Limit Region](https://000064.awsfcaj.com/vi/2-limit-region/)<br>[Limit Family](https://000064.awsfcaj.com/vi/3-limit-family/)<br>[Limit Size](https://000064.awsfcaj.com/vi/4-limit-size/) |

### ACHIEVEMENTS IN WEEK 11:
1. Froze CPAWS project features; released to production, then completed and submitted the workshop report and the internship report.
2. Deployed a complete multi-function Serverless application (including real-time image processing) with AWS SAM (Serverless Application Model).
3. Completed a Machine Learning pipeline on SageMaker: from data preparation (Data Wrangler) to training and deploying an XGBoost model.
4. Configured an AI-assisted coding environment (Amazon Q, CodeWhisperer) in VS Code to speed up coding and debugging.
5. Set strict IAM policies to control resource creation, prevent budget overruns (cost control) and ensure security.
