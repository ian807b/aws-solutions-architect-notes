# Databases on EC2

## Introduction

Although sometimes databases are run on EC2, it' generally considered bad practice in AWS.

---

## Common EC2 Database Architectures

1. **Single EC2 Instance**
   - Runs a database, an application, and possibly a web server (e.g., Apache).
2. **Split Architecture**
   - One EC2 instance runs the application/web server.
   - A second instance runs the database.

> WordPress stack for animals4life was using the **single-instance architecture**.

---

## Availability Zone (AZ) Considerations

When using multiple EC2 instances:

- **Same AZ**: No data transfer cost between instances using private IPs.
- **Different AZs (e.g., AZ-A and AZ-B)**:
  - Requires reliable cross-AZ communication.
  - Introduces small but non-zero data transfer costs.

---

## Why You _Might_ Run a Database on EC2

- **OS-Level Access Needed**
  - Only EC2 gives you full control over the OS.
  - Useful for rare tuning or debugging tasks.
  - But: Always **question** whether OS-level access is truly needed.
- **Unsupported Database or Version**

  - AWS might not support your specific DB/version.
  - Common in niche, emerging, or legacy setups.

- **Custom Architectures**

  - Replication types or deployment models unsupported by AWS services.

- **Organizational Directives**
  - Sometimes you're told to do it.

> 🔒 **Exam Tip**: There may be questions where the _correct_ choice is to use EC2.

---

## Why You _Shouldn't_ Run a Database on EC2

### 1. **Admin Overhead**

- Must manage both the EC2 instance and the DB server.
- Requires:
  - Patch management
  - Compatibility testing
  - Scheduled maintenance outside of core hours
- Adds stress and cost.

### 2. **Disaster Recovery Complexity**

- Backup strategy must be self-managed:
  - EBS snapshots
  - DB-level backups
  - External storage like S3
- Managed DB services handle much of this automatically.

### 3. **Single AZ Limitation**

- EC2 + EBS lives within one AZ.
- AZ failure = potential data unavailability.

### 4. **Missed Features**

- AWS managed database services offer:
  - Advanced performance tuning
  - Security integrations
  - Native scaling and replication
  - Automation
- You miss out on these by self-hosting.

### 5. **Lack of Serverless/Elasticity**

- EC2 is a fixed-cost resource (on/off).
- No ability to auto-scale down during idle periods.
- Minimum cost = EC2 instance rate.

### 6. **Replication Overhead**

- Manual setup and monitoring required.
- AWS managed services offer built-in replication features.

### 7. **Limited Performance Optimization**

- AWS invests heavily in optimizing their DB products.
- Self-managed EC2 DBs can't match this.

---
