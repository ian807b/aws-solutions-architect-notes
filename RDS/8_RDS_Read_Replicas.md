# RDS Read-Replicas

## 1. What Are Read Replicas?

- **Read-only** replicas of an RDS primary instance
- Created to offload **read traffic** from the primary
- Maintained via **asynchronous replication**

---

## 2. Key Use Cases

| Use Case                | Description                                                    |
| ----------------------- | -------------------------------------------------------------- |
| **Read scaling**        | Offload read operations to replicas                            |
| **Cross-region access** | Serve read traffic from regions closer to users                |
| **Disaster recovery**   | Promote replicas quickly during failures (not data corruption) |
| **Low RTO**             | Near-zero **Recovery Time Objective** when promoting a replica |

---

## 3. Comparison: Multi-AZ vs Read Replicas

| Feature         | Multi-AZ             | Read Replicas                  |
| --------------- | -------------------- | ------------------------------ |
| **Replication** | Synchronous          | Asynchronous                   |
| **Usage**       | Standby (not usable) | Read-only                      |
| **Failover**    | Automatic            | Manual promotion required      |
| **RTO**         | ~60–120s             | Near-zero                      |
| **RPO**         | Low                  | Near-zero (not for corruption) |

> For the exam:
>
> - Multi-AZ → **synchronous**
> - Read Replicas → **asynchronous**

---

## 4. Read Replica Architecture

- Replication is **asynchronous**
  - Data is written to primary, then replicated to replicas
  - Possible **replication lag** (depends on network/write volume)
- Not part of the primary instance
  - Have **separate endpoints**
  - **Applications must be explicitly configured** to use them

---

## 5. Regional Placement

- Can be created:
  - **Within the same region**
  - **Across AWS regions** (cross-region)
    - Fully managed & encrypted in transit
- Improves **global read performance** and **resilience**

---

## 6. Scaling Read Performance

- You can create **up to 5 Read Replicas** per RDS instance
- Each Read Replica can also have its **own replicas**
  - Useful for read scaling
  - ⚠️ Increases **lag** and **inconsistency risk**

---

## 7. RTO & RPO Considerations

| Term    | Meaning                  | Read Replica Benefit                  |
| ------- | ------------------------ | ------------------------------------- |
| **RTO** | Time to recovery         | Near-zero (quick promotion)           |
| **RPO** | Max acceptable data loss | Near-zero (data is almost up to date) |

> ⚠️ **Exception**:  
> Read Replicas are **not suitable** for recovery from **data corruption**  
> They will likely mirror the corrupted state

---

## 8. Promotion to Standalone Instance

- Read Replicas remain **read-only** until **promoted**
- After promotion:
  - Becomes a fully functional **independent RDS instance**
  - Can be used for both **reads and writes**
  - Ideal for **failover or regional recovery**

---
