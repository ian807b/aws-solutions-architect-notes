# 🎯 Weighted Routing in Route 53

## 📌 Summary

**Weighted Routing** allows you to control the proportion of DNS queries that are routed to different resources, based on defined weights. It is commonly used for:

- Simple load balancing
- A/B testing (e.g., testing new versions of applications)

---

## 🧱 Basic Concept

### ✅ Scenario

You have a public hosted zone — e.g., `animals4life.org` — with three `www` records:

```text
www.animals4life.org    A    10.0.0.1
www.animals4life.org    A    10.0.0.2
www.animals4life.org    A    10.0.0.3
```

### ✅ Applying Weights

Assign weights to each record:

```text
10.0.0.1 → weight = 40
10.0.0.2 → weight = 40
10.0.0.3 → weight = 20
```

Route 53 calculates the **total weight**:

- `40 + 40 + 20 = 100`

Then selects which record to return based on its proportion:

- `10.0.0.1 → 40%`
- `10.0.0.2 → 40%`
- `10.0.0.3 → 20%`

---

## ⚖️ Weight Behavior

- A record with weight `0` will **never be returned** unless **all records** have weight `0` (then all are returned).
- Weight values do **not have to sum to 100** — they are relative.
- You can use **any integer** weights (e.g., 5, 25, 70).

---

## ❤️ Use Cases

- **Simple load balancing** across multiple EC2 instances or endpoints
- **A/B testing**: send a small percentage of traffic to a new version of your application

Example: Send 5% of traffic to `v2.myapp.com` to test a new version, while 95% goes to stable `v1.myapp.com`

---

## ❤️ + 🩺 Optional Health Checks

You can **associate health checks** with weighted records.

- When Route 53 selects a record **based on weight**, it checks health
- If the selected record is **unhealthy**, it is skipped
- Health checks do **not** adjust the total weight — the process simply repeats until a healthy record is selected

---

## 🧠 Key Takeaways

- Weighted routing lets you distribute DNS traffic based on custom-defined percentages
- It’s not a replacement for full-featured load balancers but is useful for basic distribution and testing scenarios
- Works great with health checks to enhance availability and fault tolerance

---
