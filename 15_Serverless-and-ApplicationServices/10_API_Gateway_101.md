# API Gateway

## What is API Gateway?

- **API Gateway** is a **managed AWS service** for creating and managing **APIs** (Application Programming Interfaces).
- Acts as the **entry point** for clients to communicate with **backend services**.
- Example: Netflix app uses APIs to connect with its backend.

---

## Key Features

- **Highly available & scalable** (managed service)
- Handles:
  - **Authorization**
  - **Throttling**
  - **Caching**
  - **CORS support**
  - **Data transformation**
- Supports the **OpenAPI specification**
- Can directly integrate with AWS services (e.g., DynamoDB, SNS, Step Functions)

---

![API_1](/Diagrams/APIGateway-1.png)

---

## API Gateway Architecture

### Clients
- Mobile apps
- Web apps (e.g., from S3 static site)
- Other APIs

### Backend Integrations
- AWS Lambda
- HTTP endpoints (on-prem or AWS-hosted)
- Direct integration with AWS services

### Phases of API Requests
1. **Request Phase**
   - Authorize
   - Validate
   - Transform input
2. **Integration Phase**
   - Route to backend
3. **Response Phase**
   - Transform & return response

---

## Authentication Methods

| Method             | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| Open Access        | No auth (not recommended for secure APIs)                                   |
| **Cognito**        | Uses user pools; token passed to API Gateway and **natively validated**     |
| **Lambda Authorizer** | Custom logic; API Gateway invokes Lambda to validate bearer tokens        |
| IAM Auth           | Pass IAM credentials in request headers (not required for exam)             |

---

![API2](/Diagrams/APIGateway-2.png)

---

## Endpoint Types

| Type            | Description                                                                 |
|-----------------|-----------------------------------------------------------------------------|
| **Edge-Optimized** | Routes requests through CloudFront edge locations (best for global clients) |
| **Regional**       | Keeps API traffic within a specific AWS region                             |
| **Private**        | API is only accessible **inside a VPC** via **interface endpoints**         |

---

![API3](/Diagrams/APIGateway-3.png)

---
## API Gateway Stages

- A **stage** is a **deployment target** for your API configuration (e.g., `prod`, `dev`)
- Each stage:
  - Has its **own URL**
  - Can use different Lambda versions
  - Supports **individual deployment and rollback**
  - Can be used for **isolation and testing**

---

### Canary Deployments

- Canary stage is a **sub-stage** of an existing stage.
- You can route **partial traffic** to new versions:
  - Adjust traffic split
  - Test new API versions in production
  - Roll back if issues occur
  - Promote to full version when stable

---

## Important Error Codes

| Code  | Meaning               | Description                                                                 |
|-------|------------------------|-----------------------------------------------------------------------------|
| 400   | Bad Request            | Generic client-side error                                                   |
| 403   | Forbidden              | Access denied; possibly due to failed auth or WAF filtering                 |
| 429   | Too Many Requests      | **Throttling** – client exceeded request limits                             |
| 502   | Bad Gateway            | Bad response from backend (e.g., Lambda returned invalid response)          |
| 503   | Service Unavailable    | Backend service is unavailable or overloaded                                |
| 504   | Gateway Timeout        | Integration timeout (**29-second max**) – affects long-running Lambdas      |

> Note: Lambda timeout = 15 mins, **API Gateway integration timeout = 29 seconds**

---

## API Gateway Caching

- **Caching is configured per stage**
- Reduces:
  - Backend load
  - Cost
  - Latency (due to faster response times)
- Cache specs:
  - **Size:** 500 MB – 237 GB
  - **TTL:** Default = 300 sec; range = 0 to 3600 sec
  - **Encrypted cache** is supported

---

![API4](/Diagrams/APIGateway-4.png)

---

## Summary

- API Gateway is a powerful **frontend interface** for serverless and traditional applications.
- Supports multiple **authentication methods**, **endpoint types**, and **deployment workflows**.
- Integrates directly with AWS services or Lambda.
- Provides advanced **caching**, **throttling**, and **monitoring** capabilities via **CloudWatch**.
- Error codes help diagnose integration or client-side problems.
- Canary deployments and stage management help manage rollout safely.

---

