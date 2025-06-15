# Simple Notification Service (SNS)

## What is AWS SNS?
* **Pub(lish)/Sub(scribe) Messaging Service**: Highly available, durable, secure.
* **Public AWS Service**: Accessible from anywhere with network connectivity to public AWS endpoints.
* **Function**: Coordinates the sending and delivery of messages.
* **Message Size**: Payloads up to **256 KB**. (Not for large binary files like entire videos).

## Core Concepts
* **SNS Topic**: The base entity of SNS.
    * Permissions and most configuration are controlled at the topic level.
* **Publisher**: An entity that **sends messages to a topic**.
* **Subscriber**: An entity that **receives messages** from a topic. By default, subscribers receive all messages sent to the topic.

### Types of SNS Subscribers:
* **HTTP/HTTPS endpoints**
* **Email addresses**
* **SQS queues**: Each message sent to the topic is added to the queue.
* **Mobile push notification systems**: Messages delivered to mobile phones as push notifications or SMS.
* **Lambda functions**: The Lambda function is invoked when messages are sent to the topic.

## SNS in AWS Ecosystem
SNS is used across various AWS products and services:
* **CloudWatch**: Alarms can send notifications when their state changes.
* **CloudFormation**: Notifies when stacks change state.
* **Auto Scaling Groups**: Can send notifications for scaling events.

## SNS Architecture Diagram Explained
* **SNS Service**: Operates from the **AWS Public Zone**.
    * Accessible from the public internet (with relevant AWS permissions).
    * Accessible from a VPC if configured to access public AWS endpoints.
* You create **topics** within SNS.
* **Producers (Publishers)**: A wide variety of sources can publish messages into a topic:
    * External APIs
    * CloudWatch
    * EC2 instances
    * Auto Scaling Groups
    * CloudFormation stacks
    * Many other AWS services
* **Subscribers**: Receive messages from the topic. Entities can be both subscribers and producers (e.g., APIs).
    * **Filtering**: It's possible to apply a **filter policy** to a subscriber, so it only receives messages relevant to its specific functionality.

## Fan-out Architecture
* A common pattern: A **single SNS topic with multiple SQS queues as subscribers**.
* This allows a single message representing a workload to be sent to an SNS topic, and then "fanned out" to multiple SQS queues.
* Each SQS queue (and its consumers) can then perform slightly different or isolated processing of the same workload (e.g., different video sizes/bitrates for a processing job).

![SNS](/Diagrams/SNS-1.png)

## Important SNS Functionality
* **Delivery Status**: Confirms the status of message delivery to certain subscriber types (e.g., HTTP/HTTPS, Lambda, SQS).
* **Delivery Retries**: Supports reliable delivery with retry mechanisms.
* **High Availability & Scalability**: SNS is a regionally resilient service.
    * Data is replicated within a region.
    * Scalable to cope with a range of workloads (from nothing to highly transactional).
    * Highly available; continues to function if individual Availability Zones fail.
* **Server-Side Encryption (SSE)**: Any persistently stored data can be encrypted on disk, important for compliance requirements.
* **Cross-Account Usage**:
    * Like S3 buckets, SNS topics support **resource policies (topic policies)**.
    * These policies allow you to configure which AWS identities from other accounts have access to the topic.

SNS is a foundational service crucial for building application architectures in AWS.