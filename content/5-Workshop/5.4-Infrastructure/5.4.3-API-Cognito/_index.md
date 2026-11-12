---
title: "API and Authentication"
date: 2026-11-20
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---
#### Endpoints

| Endpoint | Description |
|---|---|
| `GET /health` | API health check |
| `GET /forecasts` | Latest forecasts for all 4 assets |
| `GET /forecasts/{asset}` | Latest forecast for one asset (`gold`, `silver`, `brent`, `wti`) |

All data endpoints use a **Cognito authorizer**; the client sends the ID token in the `Authorization` header.

#### Deploy

```bash
cdk deploy CpawsServing CpawsAuth CpawsApi --profile cpaws-dev \
  -c allowed_origin=http://localhost:5173
```

The User Pool **does not allow self sign-up**; an admin creates accounts for test users.

#### Call the API

Sign in on the web to get an ID token, then:

```bash
curl -H "Authorization: $ID_TOKEN" \
  https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/<stage>/forecasts/gold
```

The response includes `base_date`, `model_version` (e.g. `baseline-q252`), quantiles for h = 1–7, probability of an increase, the `fx.usd_vnd` rate and an `is_sample` flag for sample data.

#### Run the Web App Locally

```bash
cd web
npm install
npm run dev
```

**Screenshots:** ...
