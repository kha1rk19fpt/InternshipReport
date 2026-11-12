---
title: "Prerequisites"
date: 2026-11-20
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---
#### Required Tools

| Tool | Suggested version | Used for |
|---|---|---|
| Python | 3.12 | Data, model, Lambda and CDK code |
| Node.js | 20 LTS | AWS CDK CLI and the web app (React + Vite) |
| AWS CLI | latest v2 | Signing in and working with AWS |
| AWS CDK CLI | v2 | `npm install -g aws-cdk` |
| Docker (BuildKit enabled) | latest | Building Lambda container images |
| Git | latest | Source control |

#### Account and Permissions

- The AWS account uses **IAM users in groups**, signing in with short-lived sessions and no long-term access keys
- Region **ap-southeast-1 (Singapore)**
- Enable model access on Amazon Bedrock (Claude Haiku 4.5, Amazon Nova) via cross-region inference
- Create an **AWS Budgets** alert before starting

```bash
# sign in with a dedicated project profile
aws login --profile cpaws-dev
aws sts get-caller-identity --profile cpaws-dev
```

#### Secrets

- `.env` is in `.gitignore`; only `.env.example` (variable names, no values) is committed
- The EIA API key is stored in **SSM Parameter Store** as a `SecureString`

```bash
aws ssm put-parameter --profile cpaws-dev \
  --name /cpaws/dev/eia-api-key --type SecureString --value "<EIA_API_KEY>"
```

{{% notice warning %}}
Never commit API keys, passwords or full Account IDs to a public repository.
{{% /notice %}}

**Screenshots:** ...
