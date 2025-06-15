# Amazon RDS High Availability: Multi-AZ Deployments

## 1. Multi-AZ Instance Deployment (Legacy Mode)

### Overview

- Originally the only high availability (HA) option in RDS.
- Now known as **Multi-AZ Instance Deployment**.
- Consists of:
  - **Primary DB instance** (handles all reads/writes)
  - **Standby replica** in a different Availability Zone (AZ)
  - **Synchronous replication** between primary and standby.

### Key Characteristics

- **Replication Type**: Synchronous
- **Standby Access**: No reads/writes allowed from standby
- **DNS Failover**:
  - Access via a **database CNAME**
  - Failover swaps CNAME to standby (now new primary)
  - Takes **60–120 seconds**
  - Application performance may improve by **disabling DNS caching**
- **Backup Optimization**:
  - Backups are taken from standby (no impact on primary)
  - Stored in **AWS-managed S3**, replicated across multiple AZs
- **Engines and Replication Tech**:
  - _MariaDB, MySQL, Oracle, PostgreSQL_: Amazon failover tech
  - _SQL Server_: Database mirroring / Always On Availability Groups

### Failover Triggers

- AZ failure
- Primary DB instance failure
- Manual failover
- Instance type change
- OS patching or maintenance

### Limitations

- **Single standby instance**
- **Standby is idle**, not used for reads
- **Same region only** (multi-AZ = multiple AZs, not multiple regions)
- **Not included in Free Tier**

---

## 2. Multi-AZ Cluster Deployment (Modern Architecture)

### Overview

- Newer and more performant HA architecture.
- RDS deploys a **cluster**:
  - **1 writer**
  - **2 readers** (in different AZs)
- **Read replicas are usable**, unlike in instance deployment.

> ⚠️ **Do not confuse this with Amazon Aurora.**  
> While similar in some design patterns, Aurora has a fundamentally different storage architecture.

### Key Characteristics

- **Writer**: Handles writes and can serve reads.
- **Readers**:
  - Serve read traffic only
  - Require application logic to route read/write traffic separately
- **Replication**:
  - Uses **transaction logs**
  - Writes are committed when acknowledged by at least **1 reader**
- **Faster Failover**:
  - As fast as **35 seconds**, plus transaction log application time
- **Endpoints**:
  - **Cluster endpoint**: Read/write, points to the writer
  - **Reader endpoint**: Distributes reads across available readers
  - **Instance endpoints**: Specific to each DB instance (use only for testing)

### Storage Architecture

- Each instance has **local NVMe SSD** + **EBS-backed storage**
- Faster performance compared to instance deployment
- Uses **Graviton architecture** for improved efficiency

### Benefits Over Instance Deployment

| Feature                 | Instance Mode | Cluster Mode              |
| ----------------------- | ------------- | ------------------------- |
| Synchronous Replication | ✅            | ✅ (via transaction logs) |
| Read Replica Usability  | ❌            | ✅                        |
| Number of Replicas      | 1 standby     | 2 readers                 |
| Read Scalability        | ❌            | ✅                        |
| Failover Speed          | 60–120 sec    | ~35 sec                   |
| Local Storage           | ❌            | ✅ (NVMe + EBS)           |
| Scaling Reads           | ❌            | ✅ (via reader endpoint)  |

---

## Summary

| Feature            | Instance Deployment | Cluster Deployment    |
| ------------------ | ------------------- | --------------------- |
| Write Availability | ✅                  | ✅                    |
| Read Availability  | ❌                  | ✅                    |
| Reader Usage       | ❌ (standby only)   | ✅                    |
| Scaling Reads      | ❌                  | ✅                    |
| Failover Time      | 60–120 sec          | ~35 sec               |
| Replication Method | Storage-level       | Transaction log-based |
| Backup Impact      | Low (from standby)  | Low (from readers)    |

---
