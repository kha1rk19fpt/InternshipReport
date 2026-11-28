---
title: "Week 4 Worklog"
date: 2026-10-05
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:
- Master Amazon EC2: manage the instance lifecycle, create Custom AMIs, troubleshoot and recover data.
- Practice deploying real web applications (LAMP stack, Node.js) on both Linux and Windows.
- Understand storage services (S3, Storage Gateway) and the Shared Responsibility Model.
- Master the core security services: IAM, Cognito, AWS Organizations and KMS.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
|:---:|---|:---:|:---:|---|
| **Mon** | **- Practice Lab 9: Amazon Elastic Compute Cloud (EC2)**<br>+ Introduction<br>+ Preparation:<br>* Create a Linux VPC<br>* Create a Windows VPC<br>* Create a Linux Security Group<br>* Create a Windows Security Group<br>+ Launch a Windows instance<br>* Create the Windows Instance<br>* Connect to the Windows instance<br>+ Launch a Linux instance<br>* Create the Linux instance<br>* Connect to the Linux instance | 05/10/2026 | 05/10/2026 | [Lab 9 Intro](https://000004.awsfcaj.com/vi/1-introduce/)<br>[Lab 9 Prereq](https://000004.awsfcaj.com/vi/2-prerequiste/)<br>[Launch Windows](https://000004.awsfcaj.com/vi/3-launchwindowsinstance/)<br>[Launch Linux](https://000004.awsfcaj.com/vi/4-launchlinuxinstance/) |
| **Tue** | **- Continue Lab 9:**<br>+ Amazon EC2 basics<br>* Change the EC2 configuration<br>* Create an EC2 snapshot<br>* Create a Custom AMI<br>* Launch an instance from the custom AMI<br>* Access EC2-Windows after losing the Key Pair using SSM<br>* Access EC2-Linux after losing the Key Pair using User Data<br>* Configure a desktop interface for EC2-Ubuntu 22.04<br>* Amazon EBS Snapshots Archive<br>* Share an AMI | 06/10/2026 | 06/10/2026 | [EC2 Basic](https://000004.awsfcaj.com/vi/5-amazonec2basic/) |
| **Wed** | **- Continue Lab 9:**<br>+ Deploy a Node.js application on Amazon Linux<br>* Install a LAMP web server (preparation, testing, database configuration, phpMyAdmin installation)<br>* Install Node.js on Linux<br>* Deploy the application on the Linux instance<br>+ Node.js application on Amazon EC2 Windows<br>* Install XAMPP on the Windows instance<br>* Install Node.js on the Windows instance<br>* Deploy the application on the Windows instance<br>+ Limit resource usage with IAM<br>* Allow services only in a specific Region<br>* Limit EC2 usage by Instance Family<br>* Limit EC2 usage by Instance type<br>* Limit the EBS volume storage type<br>* Limit resource deletion by the company IP address<br>* Limit resource deletion by time window<br>+ Clean up resources | 07/10/2026 | 07/10/2026 | [Linux App](https://000004.awsfcaj.com/vi/6-awsfcjmanagement-linux/)<br>[Windows App](https://000004.awsfcaj.com/vi/7-awsfcjmanagement-windows/)<br>[Governance](https://000004.awsfcaj.com/vi/8-costusagegovernance/)<br>[Cleanup](https://000004.awsfcaj.com/vi/9-cleanup/) |
| **Thu** | **- Study Module 04: Storage services on AWS:**<br>+ 04-01: Amazon Simple Storage Service (S3) - Access Point - Storage Class<br>+ 04-02: S3 Static Website & CORS - Control Access - Object Key & Performance - Glacier<br>+ 04-03: Snow Family - Storage Gateway - Backup | 08/10/2026 | 08/10/2026 | [04-01](https://youtu.be/_yunukwcAwc?si=6HqDHVQJq8Ks9aWh)<br>[04-02](https://youtu.be/mPBjB6Ltl_Q?si=OITIt1x3d7OZ4ei_)<br>[04-03](https://www.youtube.com/watch?v=YXn8Q_Hpsu4) |
| **Fri** | **- Study Module 05: Security services on AWS:**<br>+ 05-01: Shared Responsibility Model<br>+ 05-02: AWS Identity and Access Management<br>+ 05-03: Amazon Cognito<br>+ 05-04: AWS Organizations<br>+ 05-05: AWS IAM Identity Center<br>+ 05-06: AWS Key Management Service<br>+ 05-07: AWS Security Hub | 09/10/2026 | 09/10/2026 | [05-01](https://youtu.be/tsobAlSg19g?si=-xSAVT8MZReV10RP)<br>[05-02](https://youtu.be/N_vlJGAqZxo?si=KxeWofBCdviRYax1)<br>[05-03](https://youtu.be/pZ2fgEFK3Vs?si=QWkbW4avA9wlvlhP)<br>[05-04](https://youtu.be/5oQY8Rogz9Y?si=WK72gwmztbXWQVKr)<br>[05-05](https://youtu.be/NW1xrMkNMjU?si=Owp-3CDDmW7YYTY4)<br>[05-06](https://youtu.be/GMihNQojhZc?si=Bt4oUqD2a5_IA0mm)<br>[05-07](https://youtu.be/clj2E0rNBEs?si=eUp1O-14XFDbpbbe) |

### Week 4 Achievements:
1. Fully completed Lab 9: proficient in advanced EC2 administration tasks (resize, snapshot, Custom AMI, recovery access).
2. Successfully deployed real applications (LAMP stack, Node.js) on EC2 Linux and Windows.
3. Practiced cost and compliance governance by limiting resource creation by Region, Instance Type and time window with IAM policies.
4. Mastered Module 04 on data storage solutions (S3, Glacier, Storage Gateway) and backup strategies.
5. Consolidated Module 05 on cloud security: the Shared Responsibility Model and identity tools (IAM, Cognito, Identity Center).
