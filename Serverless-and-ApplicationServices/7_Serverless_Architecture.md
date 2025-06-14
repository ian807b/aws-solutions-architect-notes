# Serverless Architecture – AWS Deep Dive

## What is Serverless?

- **Serverless is a software architecture**, not a hardware model.
- Despite the name, **servers are still used** — but they are fully managed by the cloud provider.
- The key goal: **minimize server management overhead** (cost, admin, risk).
- AWS heavily supports serverless through its ecosystem of managed services.

---

## Key Characteristics

- **Stateless and ephemeral** compute environments
- **Event-driven** architecture:
  - Nothing runs until triggered by a system or user event.
- Functions run in clean environments, obtain needed data, perform a task, and exit.
- **Scalability and cost-efficiency**:
  - Pay only for execution time
  - No charge when idle

---

## Architecture Inspirations

- Serverless borrows heavily from:
  - **Microservices** (fine-grained service boundaries)
  - **Event-driven** models
- Applications are broken into **small, specialized functions** (e.g., Lambda functions)

---

## Building Blocks of Serverless in AWS

| Component | Purpose |
|----------|---------|
| **AWS Lambda** | General-purpose compute (FaaS) |
| **Amazon S3** | Static website hosting, file/object storage |
| **Amazon DynamoDB** | NoSQL persistent data storage |
| **AWS Cognito** | Identity federation and credential exchange |
| **Elastic Transcoder** | Media transformation and encoding |
| **Third-party identity providers** | Offload user auth (Google, Facebook, etc.) |

---

## Example: PetTube – Serverless Video Upload App

### Use Case:  
Julie uploads her dog’s vacation videos to **PetTube**.

---

### Step-by-step Architecture Breakdown

#### 1. Frontend (Static Website + JS)
- Julie visits a **static S3 website** hosting HTML + JS
- **JavaScript runs in the browser** (no backend servers involved)

#### 2. Authentication (Cognito + Google)
- JS in Julie’s browser communicates with **Google** for auth
- Google returns an **identity token**
- JS calls **AWS Cognito**, exchanges token for **temporary AWS credentials**
- Now JS can securely call AWS services (e.g., upload to S3)

#### 3. Upload Video to S3
- Julie uploads a video of Woofy to the **"originals" S3 bucket**
- Still **no self-managed compute**

#### 4. Event Trigger → Lambda + Elastic Transcoder
- S3 event triggers a **Lambda function**
- Lambda:
  - Submits **Elastic Transcoder** jobs to generate multiple video sizes
  - Stores output in a **"transcoded" S3 bucket**
  - Writes metadata to **DynamoDB**

#### 5. Retrieve User Media
- User clicks "My Media" in the app
- JS calls **another Lambda function** (optionally via API Gateway)
- Lambda reads from DynamoDB, returns URLs to transcoded videos

![Serverless](/Diagrams/Serverless-1.png)

## Why Serverless?

- Uses **only managed services**: S3, Lambda, Cognito, DynamoDB, Transcoder
- **Cost-efficient**: Zero baseline cost; only pay on execution
- **Fully event-driven**: Resources activated on demand
- **No EC2**, no infrastructure to provision, scale, or patch
- Leverages existing identity systems (Google, etc.)
- Designed for scale, flexibility, and developer agility

---

## Notes on IAM and Limits

- Avoid using **IAM users for customer auth**:
  - AWS account limit: **5,000 IAM users**
  - Each app user would need a new IAM user account
- Use **Cognito + identity federation** instead

---

## Summary

Serverless architecture is:
- Stateless
- Event-driven
- Composed of fine-grained managed services
- Highly scalable and cost-effective
---