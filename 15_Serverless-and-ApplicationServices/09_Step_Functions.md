# AWS Step Functions – Workflow Orchestration on AWS

## Why Step Functions?

- Step Functions **address limitations** of AWS Lambda:
  - Lambda functions are **stateless** and **ephemeral**
  - Max execution time of **15 minutes**
  - Chaining Lambdas is possible but **messy at scale**

- When building **long-running processes** or needing **persistent state**, Step Functions are the better solution.

---

## What Are Step Functions?

- AWS Step Functions allow you to define **state machines** (aka workflows).
- These workflows consist of **states**, which can:
  - Do things (e.g., invoke a Lambda)
  - Make decisions
  - Wait
  - Succeed or fail

- State Machines maintain **data** throughout their execution and define a flow from **start → end**.

---

## Real-World Example: Order Fulfillment

> Think Amazon:
> - Confirm purchase
> - Locate stock
> - Pick, pack, verify
> - Book shipping
> - Dispatch order

This multi-step flow benefits from:
- Data persistence
- Decision logic
- Long-running support (up to 1 year with Standard workflows)

---

## Standard vs Express Workflows

| Type     | Use Case                                      | Duration Limit | Characteristics                     |
|----------|-----------------------------------------------|----------------|-------------------------------------|
| Standard | Long-running workflows (order processing)     | 1 year         | Default, detailed logging, retries  |
| Express  | High-volume, event-driven (IoT, mobile apps)  | 5 minutes      | Fast, cost-effective, high-throughput |

---

## Starting a State Machine

You can start executions using:

- API Gateway
- IoT rules
- EventBridge (formerly CloudWatch Events)
- Lambda functions
- Manual triggering

---

## Amazon States Language (ASL)

- State Machines are defined in **Amazon States Language (ASL)**.
- Based on **JSON**
- You'll use ASL in the upcoming **demo lesson** to build a real app.

---

## IAM Roles for Step Functions

- Step Functions use **IAM roles** to access AWS services.
- The **state machine assumes the role** during execution to call Lambda, DynamoDB, etc.

---

## Types of States

| State Type     | Description                                                                 |
|----------------|-----------------------------------------------------------------------------|
| **Succeed**    | Marks successful end of execution                                           |
| **Fail**       | Ends execution with failure                                                 |
| **Wait**       | Pauses workflow for a duration or until a date                              |
| **Choice**     | Branching logic based on input (like `if` conditions)                       |
| **Parallel**   | Executes multiple branches in parallel                                      |
| **Map**        | Iterates over a list, performing actions for each element                   |
| **Task**       | Performs work by integrating with AWS services like Lambda, ECS, Glue, etc. |

---

## Task State Integrations

Task states allow Step Functions to interact with services like:

- Lambda
- ECS
- Batch
- DynamoDB
- SNS, SQS
- Glue
- SageMaker
- EMR

---

## Demo Architecture: Pet Cuddle-a-tron

### Problem

- Whiskers (the cat) wants to remind Bob (the human) to cuddle him.
- Reminders must be flexible:
  - Can occur within minutes or hours
  - Must support **email**, **SMS**, or **both**

### Solution: Serverless Architecture

| Component         | Purpose                                                |
|------------------|--------------------------------------------------------|
| S3               | Hosts HTML + JS frontend (Bob's interface)             |
| API Gateway      | Interface between client and AWS                       |
| Lambda           | Handles API requests, invokes Step Function            |
| Step Functions   | Coordinates wait → notification logic                  |
| Lambda (task)    | Email Reminder via SES                                 |
| Lambda (task)    | SMS Reminder via SNS                                   |

---

### Workflow Breakdown

1. **Frontend** is downloaded from S3
2. **JavaScript** in Bob’s browser gathers:
   - Time until next cuddle (in seconds)
   - Custom message
   - Notification method (email/SMS/both)
   - Contact details (email address/phone number)

3. **Client-side JS → API Gateway → Lambda API Function**
4. **Lambda invokes the State Machine** with input

---

### Inside the State Machine

1. **Wait State: `timer`**
   - Pauses execution for the user-defined duration

2. **Choice State**
   - Branches to:
     - Email only → `emailReminder Lambda`
     - SMS only → `smsReminder Lambda`
     - Both → Parallel execution of both Lambdas

3. **Task States**
   - `emailReminder Lambda` sends email via SES
   - `smsReminder Lambda` sends SMS via SNS

---

![StepFunction](/Diagrams/Step_Functions.png)

