# RDS Proxy

## 🔍 Why Use RDS Proxy?

| Problem | How RDS Proxy Helps |
|--------|----------------------|
| Opening/closing DB connections is costly | Maintains a **long-lived pool** of database connections |
| Lambda functions create many short-lived connections | **Reuses pooled connections** to reduce overhead |
| Database failover is complex to handle in app code | **Abstracts failover logic** and keeps endpoint constant |
| Managing DB proxies is complex | **Fully managed** and scales automatically |

---

## ⚙️ How RDS Proxy Works

- Apps and Lambda functions connect to **RDS Proxy**, not directly to the DB.
- **RDS Proxy maintains persistent connections** to the RDS/Aurora database.
- It uses **connection pooling and multiplexing**, allowing:
  - Many client connections ↔ fewer database connections
- Multiplexing enables reuse of fewer backend connections even with high client traffic.

---

## 🏗️ Example Architecture

### Scenario:
- A VPC in **us-east-1** across 3 AZs (A, B, C)
- A **primary RDS instance** in AZ-B replicates to a **standby in AZ-C**
- App servers (EC2) and Lambda functions in web subnets (AZ-B)

### Without RDS Proxy:
- EC2 and Lambda directly connect to the RDS instance
- High connection churn → DB overhead, latency, connection limits

### With RDS Proxy:
- RDS Proxy runs across all AZs
- App servers and Lambda connect to **RDS Proxy endpoint**
- Proxy manages long-lived connections to the primary DB instance
- On failover, **RDS Proxy reconnects to new primary**, app stays connected

---

## 🎯 Benefits

| Feature | Description |
|--------|-------------|
| ✅ Connection pooling | Reduces overhead of frequent connect/disconnect |
| ✅ Multiplexing | Maps many client connections to fewer DB connections |
| ✅ High availability | Proxy runs across multiple AZs |
| ✅ Faster failover | Aurora failover time reduced by **~66%** |
| ✅ Transparent failover | App connects to same proxy endpoint throughout |
| ✅ IAM integration | Works with IAM authentication (e.g., Lambda roles) |
| ✅ TLS enforcement | Supports SSL/TLS |
| ✅ Fully managed | No need to maintain your own proxy fleet |

---

## 🚀 When to Use RDS Proxy

| Use Case | Reason |
|----------|--------|
| **Too many DB connections** | Pooling and multiplexing reduces load |
| **Lambda with RDS** | Avoids connection churn, faster response times |
| **Small/burstable instances (T2/T3)** | Conserves DB resources |
| **SaaS/multi-tenant apps** | Lower latency and higher resiliency |
| **Apps requiring DB failover tolerance** | Seamless failover handled by proxy |
| **Long-lived web apps** | Uses long-term connections efficiently |
| **Dev/Test environments** | Simpler scaling, no overhead of manual pooling logic |

---

## 🔄 RDS Proxy and Failover Behavior

- Clients stay connected to RDS Proxy during DB failover
- Proxy **reconnects to the new primary** in background
- App **doesn’t need to handle** DNS updates or retry logic
- Proxy **abstracts the failure event**

---

## 🧠 Summary

RDS Proxy helps:
- Reduce DB connection overhead
- Improve app resiliency during failovers
- Scale serverless/database-heavy applications
- Simplify application architecture

---