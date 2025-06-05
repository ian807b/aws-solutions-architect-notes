# 🔀 Route 53 – Multi-Value Routing

## 📌 Summary

**Multi-Value Routing** is a Route 53 routing policy that returns multiple IP addresses (or DNS names) in response to a DNS query — **with optional health checks**. It’s ideal when you want DNS-level **active-active** routing without needing a load balancer.

---

## 🧠 Key Concepts

- Allows **multiple records with the same name**.
- Each record:

  - Can be **health checked**
  - Typically maps to **an IP address** (A record)

- Route 53:

  - Returns **up to 8 healthy records**
  - If more than 8, returns 8 at **random**

- Clients:

  - Receive multiple healthy values
  - Choose one to connect to

---

## ✅ Use Case

Use **multi-value routing** when:

- You have **multiple resources** that can all serve requests
- You want **basic DNS-level load distribution**
- You need **health-checked responses** from DNS

🟢 **Example**:
Three web servers, each with their own IP, all able to serve traffic for `www.example.com`.

---

## 🔬 Comparison with Other Policies

| Policy          | Health Check | Multiple Records  | Behavior                                |
| --------------- | ------------ | ----------------- | --------------------------------------- |
| **Simple**      | ❌ No        | One per name      | All records returned, no health check   |
| **Failover**    | ✅ Yes       | ✅ Primary/Backup | Route only to healthy primary or backup |
| **Multi-Value** | ✅ Yes       | ✅ Many per name  | Return up to 8 healthy records randomly |

---

## 👨‍💻 Example Architecture

```
📦 Hosted Zone: animals4life.org

Records:
- www → 1.1.1.1 (✓ healthy)
- www → 2.2.2.2 (✓ healthy)
- www → 3.3.3.3 (✗ unhealthy)

🧠 Health checks applied to each.

🗳️ When queried:
→ Only 1.1.1.1 and 2.2.2.2 returned (max 8)
→ Client picks one and connects
```

---

## ⚠️ Important Notes

- Not a replacement for a **load balancer** — no session stickiness or TLS termination
- Still relies on the client to **select one IP** from the returned list
- Good for **basic availability and redundancy**

---

## ✅ When to Use

- You don’t want to configure a load balancer
- You want redundancy for simple apps
- You have a global or regional fleet of servers
- You want DNS-based failover without full-blown failover routing

---

## 🗒️ Sample DNS Records

```text
www.animals4life.org.  A   1.1.1.1 (✓ healthy)
www.animals4life.org.  A   2.2.2.2 (✓ healthy)
www.animals4life.org.  A   3.3.3.3 (✗ unhealthy)
```

🔍 Query result will include only the healthy IPs (up to 8).

---

## 🧠 Recap

Multi-Value Routing combines the simplicity of Simple Routing with health checks from Failover Routing:

✅ Health-aware
✅ Active-active design
✅ DNS-level redundancy
❌ No intelligent load balancing or traffic steering

---
