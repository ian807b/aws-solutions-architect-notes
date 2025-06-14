# AWS Database Migration Service (DMS)

## 🧭 What Is AWS DMS?

- A **managed service** that simplifies and automates **database migrations**.
- Key use cases:
  - On-prem → AWS
  - AWS RDS → Aurora
  - Cross-region migrations
- Only **one endpoint** (source or destination) must be **inside AWS**.

---

## 🛠️ Core Components

| Component | Description |
|----------|-------------|
| **Replication Instance** | EC2 instance that runs DMS software and handles replication |
| **Replication Tasks** | Defines how/what to migrate between source and target |
| **Source/Target Endpoints** | Connection configs for the source and destination databases |

---

## 🔄 Migration Types

1. **Full Load**
   - One-time copy of existing data
   - Suitable when an **outage is acceptable**
2. **Full Load + CDC** *(Change Data Capture)*
   - Full data copy + real-time replication of new changes
   - Ensures **zero-downtime** migrations
3. **CDC Only**
   - Used after initial bulk load is done via another tool
   - Only replicates ongoing changes

---

## ✅ Supported Engines

- **Relational**: MySQL, PostgreSQL, MariaDB, Oracle, Microsoft SQL Server, Amazon Aurora, Azure SQL
- **NoSQL**: MongoDB
- And more...

---

## ⚙️ Architecture Overview

```text
Source DB (On-prem or AWS) ← [Replication Task] ← Replication Instance → [Replication Task] → Target DB (must be in AWS)
