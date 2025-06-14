# AWS Lambda: Part 1 - Function as a Service (FaaS) Fundamentals

## What is AWS Lambda?

- **Function as a Service (FaaS)**: You provide specialized, short-running, and focused code.
- Lambda takes care of running your code.
- **Billing**: You are billed **only for what you consume** (duration of function execution).

## Lambda Function Basics

- A Lambda function is a piece of code that Lambda runs.
- Each Lambda function uses a **supported runtime** (e.g., Python 3.8, Ruby, Java, Go, C#, Node.js).
- When creating a Lambda function, you **define the runtime** your code uses.
- Your code is loaded and executed within a **runtime environment**, specifically created for that language/runtime version.

### Resource Allocation

- When creating a Lambda function, you define the **memory allocation** (128 MB to 10,240 MB in 1 MB steps).
- **Virtual CPU (vCPU)** allocation scales indirectly with memory:
  - 1,769 MB of memory ≈ 1 vCPU.
  - It's a linear relationship: more memory means more vCPU capacity.
- **Disk Space**: 512 MB is mounted as `/tmp` by default, scalable up to 10,240 MB. This is **temporary space** and should be assumed blank on every invocation.

## Lambda Function as a Unit of Configuration

- Think of a Lambda function as a **unit of configuration**: the code plus all associated wrappings and settings.
- At its core, it's a **deployment package** that Lambda executes.
- When invoked, the deployment package is downloaded and run in a runtime environment.

### Custom Runtimes and Layers

- You can create **custom runtimes** using **Lambda Layers** (often community-contributed, enabling languages like Rust).

### Lambda and Docker/Container Images (Exam Note)

- For the exam, if you hear "Docker," generally consider it **not Lambda** (an anti-pattern for traditional containerized computing like ECS).
- **Lambda does support using container images now**, but this is distinct from "Docker" in the traditional sense.
  - You use existing container build processes to create images specifically designed to run _within the Lambda environment_.
  - Don't confuse general "Docker container images" with "images used for Lambda."

## Statelessness and Invocation

- Conceptually, **every time a Lambda function is invoked, a new runtime environment is created**.
  - The code loads, executes, and terminates.
  - Next time, a new clean environment is created.
- Lambda functions are **stateless**: **no data is left over from a previous invocation**.
- _Architecturally, always assume your code needs to work as if it's in a brand new, clean environment every time._ (Note: There are exceptions where environments can be reused, but for foundational understanding, assume statelessness per invocation.)

## Function Timeout

- Lambda functions can run for up to **900 seconds (15 minutes)**.
- This is known as the **function timeout**.
- **Critical for the exam**: For anything beyond 15 minutes, you cannot use Lambda directly for a single invocation. Other services like Step Functions are needed for longer workflows.

## Security

- Security for a Lambda function is controlled using **execution roles**.
- These are **IAM roles** assumed by the Lambda function, granting permissions to interact with other AWS products and services.
- Any permissions a Lambda function needs are provided by creating and attaching an execution role.

## Common Uses of Lambda

1.  **Core of Serverless Applications**: Often combined with **S3** and **API Gateway** to deliver serverless web applications.
2.  **File Processing (S3 Events)**:
    - Example: Watermarking images. Image uploaded to S3 -> S3 event -> invokes Lambda function to apply watermark -> terminates. Billed only for compute during invocation.
3.  **Database Triggers (DynamoDB Streams)**:
    - Invokes Lambda anytime data is inserted, modified, or deleted from a DynamoDB table with streams enabled.
4.  **Serverless Cron (EventBridge/CloudWatch Events)**:
    - Invoke Lambda functions at specific times (daily, weekly) to perform scripted activities that traditionally ran on EC2. Billed only for execution time.
5.  **Real-time Stream Data Processing (Kinesis Streams)**:
    - Lambda can be configured to invoke when data is added to a Kinesis stream.
    - Highly scalable, ideal for processing large quantities of streaming data in real-time.
