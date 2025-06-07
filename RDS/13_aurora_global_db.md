# Aurora Global Database

## 1. What Is Aurora Global Database?

- Enables **global-level replication** from one **primary region** to up to **five secondary regions**
- Each region uses an Aurora cluster
- Replication is **one-way** (from primary to secondaries)
- Use cases:
  - **Cross-region disaster recovery**
  - **Global read-scaling**
  - **High availability (HA) and business continuity (BC)**

---

## 2. Architecture Overview

### Primary Region (e.g., `us-east-1`)

- Contains:
  - **1 read/write primary instance**
  - Up to **15 read replicas**
- Standard Aurora cluster behavior

### Secondary Regions (e.g., `ap-southeast-2`)

- Each secondary:
  - Up to **16 read-only replicas**
  - Can be **promoted** to read/write during failover

### Replication

- Occurs at the **storage layer**
- **~1 second** replication lag or less
- **No performance impact** on primary DB

---

## 3. Key Benefits

| Benefit | Description |
|--------|-------------|
| **Disaster Recovery** | Promote secondary region as new primary in the event of regional failure |
| **Low RPO/RTO** | Storage-level replication ensures near real-time synchronization |
| **Global Read Scaling** | Deploy read replicas near international users for low-latency access |
| **Performance Efficiency** | No CPU overhead for replication; done at the storage layer |
| **Scalability** | Up to **5 secondary regions**, each with **16 replicas** |

---

## 4. Summary

| Feature | Value |
|--------|-------|
| Primary Cluster | 1 writer + up to 15 readers |
| Secondary Cluster | Up to 16 read-only replicas |
| Max Secondary Regions | 5 |
| Replication Lag | ~1 second |
| Replication Type | One-way (primary → secondary) |
| Promotion | Secondary clusters can be promoted during failover |
| Use Cases | DR, global scaling, performance, continuity |


## When to Use Aurora Global Database

- You need **cross-region failover** capabilities
- You want to serve **international users** with low-latency reads
- You require **resilient, multi-region architecture** with minimal replication delay

