# Aurora Serverless

> 🎥 This lesson covers **Aurora Serverless**, a flexible, auto-scaling version of Amazon Aurora that removes the need to manage provisioned database instances.

---

## 1. What Is Aurora Serverless?

- Aurora Serverless is to Aurora what **Fargate** is to ECS:
  - No need to **provision instance sizes**
  - No instance management overhead
- It brings Aurora closer to **Database-as-a-Service** (DBaaS)

---

## 2. Aurora Provisioned vs. Aurora Serverless

| Feature | Aurora Provisioned | Aurora Serverless |
|--------|---------------------|-------------------|
| Compute Management | Manually provisioned DB instances | Dynamically scaled Aurora Capacity Units (ACUs) |
| Billing | Per instance (hourly/secondly) | Per ACU (secondly) + storage |
| Auto-Pause | ❌ | ✅ (can scale to 0) |
| Scaling | Manual (resizing required) | Auto-scales based on load |
| Ideal For | Predictable workloads | Variable or unpredictable workloads |

---

## 3. Aurora Capacity Units (ACUs)

- ACU = bundled **CPU + memory**
- You set:
  - **Minimum ACUs**
  - **Maximum ACUs**
- Aurora scales **between min and max** based on usage
- Can scale down to **zero**, entering paused mode (only storage is billed)

---

## 4. Architectural Components

### Shared Cluster Storage (Same as Aurora Provisioned)

- Replicated across **6 nodes in 3 AZs**
- Max size: **128 TiB**
- Shared between compute layers

### Stateless Compute (ACUs)

- Allocated from a **warm pool** managed by AWS
- ACUs are **stateless** and **ephemeral**
- Scale **in and out** dynamically

### AWS-Managed Proxy Fleet

- Client connections go through a **shared proxy fleet**
- Ensures:
  - Zero disruption during scale events
  - No direct coupling between clients and compute nodes

---

## 5. Billing Model

| Resource | Pricing |
|----------|---------|
| **Compute (ACUs)** | Billed per second (after 10-min min) |
| **Storage** | Billed per GB-month |
| **Paused Mode** | Only storage billed |
| **Backup** | Free up to size of DB cluster |
| **I/O Requests** | Billed per request |

---

## 6. Ideal Use Cases

### ✅ Infrequent Workloads
- E.g., low-traffic blog or archive application
- Database can auto-pause when not in use

### ✅ New Applications
- Avoid sizing prematurely
- Let Aurora scale as usage patterns emerge

### ✅ Variable Workloads
- E.g., workloads with short but regular peak periods
- Aurora scales based on current demand

### ✅ Unpredictable Workloads
- No clear usage pattern
- Set a wide ACU range (e.g., 1–32) to handle any load

### ✅ Dev/Test Environments
- Automatically pause and resume
- Cost-effective when not in constant use

### ✅ Multi-Tenant SaaS Apps
- Aligns infrastructure cost with customer usage and revenue

---

## 7. Limitations / Considerations

- ❌ **Not available in the Free Tier**
- 🔄 **Application connections go through proxies**
  - Required to enable seamless scaling
- 🧠 **Exam exposure is limited** (for now), but foundational understanding helps future-proof learning

---

## 8. Summary: Aurora Serverless Highlights

| Feature | Description |
|--------|-------------|
| Auto-scaling | Yes, based on ACUs |
| Pause/Resume | Yes |
| Compute Billing | Per second |
| Storage Billing | Per GB-month |
| Proxy Layer | Manages connections to dynamic compute |
| Replication | 6-way, 3-AZ (same as provisioned) |
| Use Cases | Infrequent, unpredictable, or bursty workloads |

---

## Final Notes

- Aurora Serverless offers **low-maintenance**, **cost-effective**, and **scalable** database deployments.
- It removes the burden of capacity planning while retaining Aurora’s **resilience and performance**.
- While not yet heavily emphasized in the exam, **comparing it with Aurora Provisioned and RDS** is key to answering architectural scenario questions effectively.

> 🧠 Tip: Think of **Aurora Serverless** when you're presented with unpredictable or intermittent workload scenarios in exam questions.
