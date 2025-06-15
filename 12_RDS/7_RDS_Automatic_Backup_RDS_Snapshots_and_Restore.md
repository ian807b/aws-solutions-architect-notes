# RDS Automatic Backup, RDS Snapshots and Restore

## 1. Types of Backups in RDS

RDS provides **two types** of backup mechanisms:

### A. Automated Backups

- Automatically enabled (unless retention period is set to `0`)
- Stored in **AWS-managed S3 buckets** (invisible in your S3 console)
- Includes:
  - Daily snapshots
  - **Transaction logs** (written every 5 minutes)

### B. Manual Snapshots

- Initiated manually or via automation (e.g., scripts)
- Persist **until deleted manually**
- Like EBS snapshots:
  - First snapshot = full copy
  - Subsequent = incremental (changed blocks only)

---

## 2. Backup Storage Characteristics

- Stored in **S3** (AWS-managed, regionally resilient across AZs)
- **Not visible in your account’s S3 buckets**
- Can be replicated to **another region** (manual setup required)

> ⚠️ **Cross-region backup replication is not enabled by default**  
> You must configure it in the **automated backup settings**

---

## 3. Performance Impacts

| Deployment Mode | Backup Source | Impact               |
| --------------- | ------------- | -------------------- |
| Single AZ       | Main instance | IO pause possible    |
| Multi-AZ        | Standby       | Minimal to no impact |

---

## 4. Snapshot Behavior

- Manual process
- Stored until **explicitly deleted**
- Outlive the RDS instance (you can restore even after deletion)
- Helps meet **low RPO** by increasing frequency
- Example: hourly snapshots = ≤1 hour of data loss

---

## 5. Automated Backup Behavior

- Daily snapshot + 5-minute **transaction log updates**
- Occurs during a **backup window**
  - Chosen by AWS or configured by you
- Retention period: **0 to 35 days**
  - `0` = disabled
  - `35` = max retention
- Backups are **auto-deleted** after the retention window

### Point-in-Time Recovery (PITR)

- Combines:
  - Most recent snapshot
  - Transaction logs since that snapshot
- Allows restore to any point (5-minute granularity) within retention window

---

## 6. Deletion Behavior

| Action                             | Outcome                                              |
| ---------------------------------- | ---------------------------------------------------- |
| Delete RDS instance (no snapshot)  | Data gone unless you retained automated backups      |
| Delete RDS + create final snapshot | Snapshot persists until manually deleted             |
| Retain automated backups           | Retained only until the **retention period expires** |

---

## 7. Restore Behavior

### Manual Snapshot Restore

- Creates a **new RDS instance**
- Restores **only to the time of snapshot**
- RPO depends on how recent the snapshot is

### Automated Backup Restore

- Also creates a **new RDS instance**
- Can restore to **specific time (PITR)**
  - Snapshot → replay transaction logs → target time
- Offers **better RPO** than manual snapshots

> You must **update application endpoints** after restoring — the new instance will have a different DNS name.

---

## 8. Cost Implications

- **Storage costs** for:
  - Manual snapshots
  - Extended retention of automated backups
  - Cross-region replication
- Frequent snapshots = more storage = higher cost

---

## 9. RTO & RPO Considerations

| Metric                             | Description                                                                                                   |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| **RPO (Recovery Point Objective)** | Max acceptable data loss. Use frequent snapshots or PITR for low RPO                                          |
| **RTO (Recovery Time Objective)**  | Time to restore and resume service. RDS restore can take **significant time**, especially with large datasets |

> 💡 **Read replicas** are a strategy to improve RTO in disaster recovery.

---

## Summary Table

| Feature                       | Manual Snapshot | Automated Backup              |
| ----------------------------- | --------------- | ----------------------------- |
| Created Manually?             | ✅              | ❌                            |
| Retained Manually?            | ✅              | ❌ (auto-deleted)             |
| Snapshot Time                 | Fixed           | User-defined (PITR)           |
| Transaction Logs              | ❌              | ✅ (5-minute granularity)     |
| Visible in S3?                | ❌              | ❌ (stored in AWS-managed S3) |
| Cross-region Support          | ✅ (if enabled) | ✅ (if configured)            |
| Restore Creates New Instance? | ✅              | ✅                            |

---
