# Aurora Multi-Master Writes

## 1. Default: Aurora Single-Master Mode

| Feature | Description |
|--------|-------------|
| Write Access | Only **one** instance (the writer) |
| Read Access | 0–15 **read replicas** |
| Endpoints | **Cluster endpoint** (read/write) & **Reader endpoint** (read-only, load-balanced)
| Failover | Requires **promotion** of a replica to writer; **takes time**

---

## 2. Aurora Multi-Master Overview

| Feature | Description |
|--------|-------------|
| Write Access | **Multiple** DB instances (multi-writer) |
| Read Access | All instances support **read and write** |
| Endpoint | ❌ No load-balanced cluster endpoint — app must connect to specific instances |
| Storage | Same shared Aurora storage (128 TiB, 6-way AZ replication) |
| Failover | **Near-zero** disruption if app is designed correctly |

---

## 3. Multi-Master Write Architecture

### Write Path
1. A node receives a **write** request
2. Proposes write to **shared storage**
3. **Quorum of storage nodes** must agree to the write
4. If quorum accepts → commit to shared storage
5. If quorum rejects → error is returned

### Cache Sync
- Once write is committed:
  - It’s **replicated to other nodes’ in-memory caches**
  - Ensures **read consistency** across all writers

---

## 4. Architecture Behavior Comparison

### Single-Master Cluster

- Primary fails → cluster endpoint is updated → failover takes time
- Temporary **interruption of write access**

### Multi-Master Cluster

- Multiple active writer nodes
- App connects to multiple endpoints (or detects failure)
- Failover can be **application-handled instantly**
- Near **fault-tolerant behavior**

---

## 5. Application Design for Multi-Master

| Requirement | Description |
|-------------|-------------|
| Connection logic | Must connect to **multiple writers** manually |
| Load balancing | Must be handled in the **application layer** |
| Fault tolerance | Application should be ready to **redirect** traffic if a node fails |
| No built-in failover endpoint | ❌ No cluster-level endpoint abstraction

---

## 6. Benefits of Aurora Multi-Master

- **High Availability**
  - Failover occurs **without promotion delay**
- **Reduced Downtime**
  - Writes can continue via another writer
- **Resilience**
  - Enables near fault-tolerant applications
- **Improved Write Throughput**
  - More than one node can accept writes

> **Limitation**: More complex application logic needed (manual connection/load balancing)

---

## Use Cases

- Ideal for:
  - **Mission-critical systems**
  - **Highly available architectures**
  - **Write-heavy or distributed applications**
