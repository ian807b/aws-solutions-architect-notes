# AWS Lambda In-Depth – Part 3

## Lambda Invocation Models

There are **three types of Lambda invocations**:

---

### 1. **Synchronous Invocation**

- Caller waits for Lambda to finish and return a result.
- Common sources:
  - CLI
  - API Gateway
  - Application code
- Examples:
  - Web apps invoking Lambda through API Gateway.
- Client is responsible for retries and error handling.

> Used for **interactive, user-facing** operations.

---

### 2. **Asynchronous Invocation**

- Caller (e.g., S3, SNS) **does not wait** for Lambda to finish.
- Lambda handles:
  - Retries (0–2 times, configurable)
  - Failures (send to DLQ or **Lambda destinations**)
- Requires **idempotent** function logic
  - Same input → same effect, regardless of invocation count

> Used for **event-driven** processing (e.g., S3 uploads triggering Lambda).

#### Lambda Destinations

- You can define **destinations for success/failure**:
  - SQS
  - SNS
  - Another Lambda
  - EventBridge

---

### 3. **Event Source Mapping**

- Used when the source **doesn't push events** (e.g., Lambda function needs to be polling)
- Common sources:
  - **Kinesis**
  - **DynamoDB Streams**
  - **SQS**
  - **Amazon MSK (Kafka)**

#### How it works:

- Event source mapping polls the source
- Retrieves **batches** of records
- Sends to Lambda for processing

> Lambda needs **IAM permissions** to access the source — since the event source mapping uses the Lambda's execution role to poll the service.

#### Batch Considerations:

- Must complete processing within **15 minutes**
- Unprocessed batches can be sent to:
  - SQS
  - SNS

---

## Lambda Versions and Aliases

### Lambda Versioning

- A **version** includes:
  - Code
  - Configuration
  - Environment variables
- Immutable once published
- Each version has a **unique ARN**

### `$LATEST`

- Always points to the **latest unpublished version**
- Not immutable

### Aliases

- Named pointers to specific versions (e.g., `dev`, `stage`, `prod`)
- Can be updated to point to new versions
- Useful for blue/green deployments

---

## Cold Starts vs. Warm Starts

### Cold Start (First Invocation)

- Occurs when Lambda creates a new **execution context**:
  - Allocates hardware
  - Installs runtime (e.g., Python 3.8)
  - Downloads and installs deployment package
- Adds latency — may take **hundreds of ms or more**

### Warm Start (Subsequent Invocations)

- If invoked again **soon after**, same execution context may be reused
- Avoids setup time → **faster** (few ms)

### Provisioned Concurrency

- Keeps Lambda execution environments **pre-warmed**
- Ensures consistent performance
- Useful for:
  - High traffic periods
  - Production launches

---

## Optimizing Lambda Execution

- Lambda functions run inside **execution contexts** (like lightweight containers)
- You can:
  - Define variables **outside the handler** (shared across invocations in same context)
  - Use `/tmp` (512MB) for **cached/shared data**

> Execution contexts **are not guaranteed** to persist  
> Functions must always work with a **fresh context** if needed

---
