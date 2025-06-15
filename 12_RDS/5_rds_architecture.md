# Amazon RDS: Architecture Overview

## What is RDS?

- RDS is **not** a "Database-as-a-Service (DBaaS)" in the traditional sense.
- Instead, it's better described as **Database Server-as-a-Service**:
  - You receive a **database server instance**, not just a single database.
  - Multiple databases can be hosted on a single RDS instance.

---

## Key Characteristics

- **Managed environment**: AWS handles hardware, OS, DB engine installation, and much of the maintenance.
- **Database engines supported**:
  - **Open Source**: MySQL, MariaDB, PostgreSQL
  - **Commercial**: Oracle, Microsoft SQL Server
  - Licensing implications apply for commercial engines.

> ⚠️ **Aurora ≠ RDS**  
> Aurora is a separate, AWS-designed database product with different architecture and capabilities. Do **not** treat it as equivalent to RDS.

---

## RDS Access Control

- No SSH or OS-level access to RDS instances.
- Exception: **RDS Custom** provides some lower-level access (covered separately).

---

## RDS Architecture: Visual Model

- **Runs inside a VPC**, not publicly accessible by default.
- Operates within subnets across **Availability Zones (AZs)** in a given region.
- Supports **multi-region** components like cross-region read replicas and backups.

### RDS Subnet Groups

- A **DB Subnet Group** is a list of subnets across one or more AZs that RDS can use.
- Best practice: use one DB subnet group per RDS deployment for better flexibility.
- Example:
  - `DB Subnet Group A`: 2 public subnets
  - `DB Subnet Group B`: 3 private subnets

> 💡 **Tip**: RDS instances launched into public subnets can be made internet-accessible (but, bad practice).

---

## Multi-AZ Deployments

- When launching an RDS instance with Multi-AZ enabled:
  - One AZ hosts the **primary instance**
  - Another AZ hosts the **standby**
- AWS chooses subnets randomly unless explicitly defined.
- **Replication is synchronous**: Standby has an exact real-time copy of the primary.

---

## Connectivity

RDS instances in **private subnets** are accessible from:

- Inside the same VPC
- On-premises networks via VPN or Direct Connect
- Peered VPCs

---

## Storage Model

- **Each RDS instance has dedicated EBS-based storage**
- Even in Multi-AZ:
  - Primary and standby instances have separate EBS volumes
- Different from **Aurora**, where storage is shared across nodes

---

## Read Replicas

- Use **asynchronous replication**
- Can be created in:
  - The same region
  - Other AWS regions
- Use cases:
  - Scaling read workloads
  - Disaster recovery

---

## Backups

- Backups are stored in an AWS-managed **S3 bucket**
- These S3 buckets are not visible in your account
- **Multi-AZ mode**: backups are taken from the standby instance (no performance impact)
- Backups are **AZ-resilient**

---

## RDS Cost Model

RDS pricing is based on **allocated resources**, not actual usage:

| Component               | Description                                                                                    |
| ----------------------- | ---------------------------------------------------------------------------------------------- |
| **Instance size/type**  | Larger and feature-rich = higher hourly cost (billed per second)                               |
| **Multi-AZ deployment** | Adds cost (2+ instances)                                                                       |
| **Storage (EBS)**       | Monthly per-GB fee; provisioned IOPS storage costs more                                        |
| **Data transfer**       | In/out to internet or other regions incurs per-GB cost                                         |
| **Backups**             | Snapshot storage up to the DB storage size is free. Extra snapshot storage billed per GB-month |
| **Licensing**           | Additional charges for commercial DB engines (Oracle, SQL Server)                              |

---
