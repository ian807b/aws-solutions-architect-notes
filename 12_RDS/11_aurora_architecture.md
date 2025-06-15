# Aurora Architecture

## 1. Aurora Is Cluster-Based

- Aurora introduces the concept of a **cluster**, which includes:
  - **1 primary instance** (writer)
  - **0–15 replicas** (readers)

### Key Benefits

- **Aurora replicas**:
  - Can be used for **read operations**
  - Also serve as **failover targets**
  - Provide both **read scaling** and **availability**
- Unlike RDS standby replicas (unusable for reads), Aurora replicas are **active**.

---

## 2. Shared Cluster Storage Architecture

- Separation of compute and storage
- Aurora does **not** use instance-local EBS volumes.
- Instead, it uses a **shared cluster volume**:
  - SSD-based
  - Max size: **128 TiB**
  - **6-way replication** across **3 Availability Zones** (even when there's only 1 primary instance; i.e., 0 replicas)
  - Shared by all compute nodes (writer + readers)

### Storage-Level Replication

- **Synchronous writes** to 6 replicas
- **No burden on instances** during replication
- **Self-healing** from disk failures using remaining copies

---

## 3. High Availability and Fast Failover

- Aurora supports up to **15 replicas**
- Any replica can be promoted during failover → **faster failover** than RDS
- No storage failover process required (shared volume is always active)

---

## 4. Storage Billing and High-Water Mark

- Aurora storage is **auto-scaling** — no pre-allocation required
- Charges are based on **maximum storage consumed** (high-water mark)
  - E.g. if usage peaks at 50 GiB, billing remains at 50 GiB even if usage drops
  - To reduce charges: **create a new cluster and migrate**

---

## 5. Endpoint Architecture

| Endpoint Type         | Use                                  |
| --------------------- | ------------------------------------ |
| **Cluster endpoint**  | Points to writer (read/write)        |
| **Reader endpoint**   | Load-balances across all readers     |
| **Custom endpoint**   | Optional; maps to selected instances |
| **Instance endpoint** | Specific to each DB instance         |

- Makes read scaling and connection management easier
- Reader endpoint dynamically adjusts when replicas are added/removed

---

## 6. Performance Features

- SSD storage by default → **high IOPS**, **low latency**
- Aurora offers significantly better performance than micro RDS instances
- Aurora doesn't support **Free Tier** because it doesn't offer micro instance types

---

## 7. Aurora Cost Model

| Cost Component | Notes                                                           |
| -------------- | --------------------------------------------------------------- |
| **Compute**    | Billed per second (10-min minimum)                              |
| **Storage**    | Billed per GiB-month (based on high-water mark)                 |
| **I/O**        | Billed per I/O request                                          |
| **Backups**    | Free up to storage size (e.g. 100 GiB DB → 100 GiB backup free) |

---

## 8. Aurora Backups

- Similar to RDS:
  - **Automatic backups**
  - **Manual snapshots**
- **Restores create a new cluster**
- Storage is replicated across AZs → minimizes the need for restores

---

## 9. Aurora Exclusive Features

### A. Aurora Backtrack

- **Roll back in place** to a previous point in time
- No need to restore a snapshot
- Keeps existing endpoints, reduces downtime
- Must be **enabled per cluster**

### B. Aurora Fast Clone

- Create a new DB instance **without duplicating all storage**
- The clone shares the same underlying cluster volume as the source
- Stores only the **differences** between original and clone
- Fast and storage-efficient
- Ideal for dev/test and experimentation

---

## 10. Summary: Aurora vs Traditional RDS

| Feature              | Traditional RDS             | Aurora                                |
| -------------------- | --------------------------- | ------------------------------------- |
| Storage Type         | Instance-local EBS          | Shared cluster volume                 |
| Replicas             | 1 standby + 5 read replicas | Up to 15 active replicas              |
| Replica Usability    | Standby = idle              | Readers = active for reads            |
| Scaling              | Manual, instance-based      | Seamless, fast, cluster-aware         |
| Failover             | Slower, 60–120s             | Fast (any replica), no storage switch |
| Backup Storage       | Manual configuration        | Auto-included up to DB size           |
| Backtrack / Clone    | ❌                          | ✅ Aurora-only                        |
| Free Tier            | ✅ (micro only)             | ❌                                    |
| Endpoint Flexibility | Limited                     | Cluster, reader, custom, instance     |

---
