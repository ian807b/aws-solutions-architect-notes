# AWS Backup

## 🧾 Overview

**AWS Backup** is a fully managed, centralized data protection service. It simplifies backup and restore operations across many AWS services and accounts.

- **Centralized** management across services, accounts, and regions
- Supports **auditing**, **retention policies**, **cross-region backup**, and **compliance controls**

---

## 🎯 Key Benefits

| Feature                        | Description |
|-------------------------------|-------------|
| **Centralized Management**     | Backup and restore operations in one place |
| **Multi-Account Support**      | Integrated with AWS Organizations / Control Tower |
| **Cross-Region Copying**       | For additional protection and compliance |
| **Multi-Service Integration**  | Automates backup across many AWS services |

---

## 📦 Supported Services

- **Compute**: EC2, VMware on AWS
- **Block Storage**: EBS
- **File Storage**: EFS, FSx (Windows, Lustre, ONTAP, OpenZFS)
- **Databases**: Aurora, RDS, DynamoDB, Neptune, DocumentDB
- **Object Storage**: S3 (recently supported)

---

## 🧱 Core Components

### 1. **Backup Plans**

- Define **frequency**: hourly, daily, weekly, monthly (via **cron expressions**)
- Set **backup windows** (when backups should occur)
- Enable **continuous backups** (for point-in-time restore)
- Configure **lifecycle rules**:
  - Transition to cold storage
  - Set expiration
  - Cold storage requires **90-day minimum retention**
- Define **destination vaults**
- Enable **cross-region backup copy**

---

### 2. **Backup Resources**

- Define **what is being backed up** (e.g., RDS instance, S3 bucket)

---

### 3. **Backup Vaults**

- Where backups are **stored**
- By default: read/write enabled
- Optional: **Vault Lock** mode (WORM)
  - **Write Once Read Many**
  - **72-hour cool-off period**
  - No deletions possible once enforced
  - Designed for **compliance use cases**
  - Data still expires based on defined retention

---

### 4. **On-Demand Backups**

- Not limited to backup plans
- Can initiate manual backups any time

---

### 5. **Point-in-Time Recovery**

- Available for:
  - **RDS**
  - **S3**
- Restore to **any timestamp** within retention window

---

## 🛡️ Security and Compliance

- Vault Lock = **compliance-grade immutability**
- Audit/control backup actions with **AWS CloudTrail**
- Supports **encryption** and **access policies**

---
