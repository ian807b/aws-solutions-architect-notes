# ⚖️ Evolution of Elastic Load Balancer (ELB)

## 🎯 Purpose of This Lesson

This lesson introduces the **evolution** of the Elastic Load Balancer (ELB) product in AWS, focusing on the **three types** currently available and what you should use **today**. Further details will be covered in subsequent lessons.

---

## 🧬 ELB Overview

- **Elastic Load Balancer (ELB)**: Umbrella term for all AWS load balancers.
- Split into:
  - **Version 1**: Classic Load Balancer (CLB)
  - **Version 2**: Application Load Balancer (ALB) and Network Load Balancer (NLB)

---

## ❌ Version 1: Classic Load Balancer (CLB)

| Feature                     | Description |
|-----------------------------|-------------|
| **Introduced**              | 2009 |
| **Protocols Supported**     | HTTP, HTTPS, TCP |
| **Layer**                   | Layer 4 primarily (some Layer 7 support but limited) |
| **Limitations**             | No advanced HTTP routing, only 1 SSL cert per LB, expensive and outdated |
| **Use Today?**              | **No** – should migrate off CLB to version 2 load balancers |

> 🛑 Avoid using CLB for new deployments and migrate to ALB/NLB where possible.

---

## ✅ Version 2 Load Balancers

### 1. Application Load Balancer (ALB)

| Property         | Details |
|------------------|---------|
| **Layer**        | Layer 7 (Application layer) |
| **Protocols**    | HTTP, HTTPS, WebSocket |
| **Use Case**     | Web applications, microservices, content-based routing |
| **Routing**      | Advanced request routing via **rules** (host/path-based) |
| **Modern Web?**  | **Yes** – default choice for HTTP/HTTPS apps |

### 2. Network Load Balancer (NLB)

| Property         | Details |
|------------------|---------|
| **Layer**        | Layer 4 (Transport layer) |
| **Protocols**    | TCP, TLS (secure TCP), UDP |
| **Use Case**     | Non-HTTP apps like SMTP, SSH, gaming, VoIP |
| **Performance**  | Extremely fast, supports millions of requests per second |

> 🧠 **Version 2 load balancers (ALB, NLB)** are more feature-rich, cost-efficient, and support target groups & routing rules.

---

## 📝 Exam Tips

- **Default to version 2**: Always prefer **ALB** or **NLB** for any new architecture.
- **ALB vs NLB**:
  - ALB = HTTP, HTTPS → Application logic and content routing
  - NLB = TCP, TLS, UDP → Low-latency or non-HTTP workloads
- **CLB is deprecated**: Avoid unless explicitly stated in legacy context.

---

## ✅ Summary

| Load Balancer | Layer | Protocols          | Use Case                    |
|----------------|-------|--------------------|------------------------------|
| **CLB (v1)**    | 4/7   | HTTP, HTTPS, TCP   | ❌ Outdated – avoid using    |
| **ALB (v2)**    | 7     | HTTP, HTTPS, WS    | ✅ Web apps, microservices    |
| **NLB (v2)**    | 4     | TCP, TLS, UDP      | ✅ Non-HTTP, high performance |

> This was an **intro lesson**. Coming up next: detailed breakdowns of ALB and NLB.

