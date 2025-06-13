# Regional and Global AWS Architecture

## Three Types of Architectures

1. **Single-Region Architecture**
   - Only operates within one region or country
2. **Regional Architecture with Disaster Recovery (DR)**
   - Operates in one region with **failover** to another
3. **Multi-Region Architecture**
   - Actively operates across **multiple regions** for availability and fault tolerance

---

## 🌐 Global Architecture Components

| Component                     | Description |
|------------------------------|-------------|
| **Service Discovery (DNS)**   | Route 53 is used to resolve domain names and route users to the best region, optionally based on **health checks** and **latency-based routing** |
| **Content Delivery**          | **CloudFront** caches and delivers data closer to users |
| **Global Health Checks**      | Detect regional failures and **failover** traffic to alternate regions automatically |

> Example: Netflix users in the US resolve netflix.com via Route 53 to the **primary US-East-1** region, with **failover** to an **Australia** region.

---

## 📍 Regional Architecture Components

Once traffic enters a region, we zoom into a **regional architecture** consisting of the following tiers:

### 1. **Web Tier**
- Entry point for customer requests
- Services: **Application Load Balancer (ALB)**, **API Gateway**
- Abstracts customers from underlying infrastructure

### 2. **Compute Tier**
- Runs application logic
- Services:
  - **EC2**
  - **Lambda**
  - **ECS (Elastic Container Service)**

### 3. **Storage Tier**
- Persistent file/block storage
- Services:
  - **EBS** (Elastic Block Store)
  - **EFS** (Elastic File System)
  - **S3** (Object Storage)

### 4. **Caching Layer**
- Reduces database load and improves performance
- Services:
  - **ElastiCache**
  - **DAX** (DynamoDB Accelerator)

### 5. **Database Tier**
- Manages structured and semi-structured data
- Services:
  - **RDS**
  - **Aurora**
  - **DynamoDB**
  - **Redshift**

### 6. **Application Services Tier**
- Provides additional functionality and integration patterns
- Services:
  - **SQS** (Simple Queue Service)
  - **SNS** (Simple Notification Service)
  - **Kinesis**
  - **Step Functions**

---

## 🚦 Architecture Thinking Tips

- **Global perspective**: Focuses on **failover**, **DNS routing**, **content delivery**, and **regional isolation**
- **Regional perspective**: Focuses on **tiered architecture**, **resilience**, **scaling**, and **inter-service communication**
- Most AWS services are **regional**, so solutions architects should design platforms **regionally first**, then build global strategies

---

## 🧱 AWS Architecture Tiers Summary

| Tier             | Purpose                                          | Examples |
|------------------|--------------------------------------------------|----------|
| Web Tier         | Entry point for requests                         | ALB, API Gateway |
| Compute Tier     | Processes logic and business rules               | EC2, Lambda, ECS |
| Storage Tier     | Stores files and objects                         | EBS, EFS, S3 |
| Caching Tier     | Reduces read pressure on DBs                     | ElastiCache, DAX |
| Database Tier    | Stores structured or semi-structured data        | RDS, Aurora, DynamoDB |
| Application Tier | Enables integration and advanced functionality   | SQS, SNS, Kinesis, Step Functions |

---

## ✅ Final Thoughts

- Most AWS services are **regional**, not global
- Think of architecture as **collections of regions** forming the larger system
- Understand **which services are global** (e.g., Route 53, CloudFront) and **which are regional**
- Prepare to design using **tiers**, separating concerns and aligning services with each tier

> 🎓 This is an introduction. Upcoming lessons will go deeper into each component and architecture pattern.
