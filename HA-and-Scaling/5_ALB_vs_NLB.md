# ALB vs. NLB – AWS Load Balancer Deep Dive

## Why Move Away from Classic Load Balancers?

### Classic Load Balancer (CLB) Limitations:

- No support for **SNI (Server Name Indication)**.
  - SNI: An extension to the Transport Layer Security (TLS) protocol that allows a client to tell the server which hostname (domain name) it's trying to connect to at the start of the handshake.
- Only **one SSL certificate** per CLB.
- Cannot consolidate multiple HTTPS apps under one LB.
- Requires **a separate CLB per HTTPS app**.

### ALB Advantage – Consolidation:

- Supports **multiple SSL certificates** via **SNI**.
- Uses **listener rules** to route traffic to different target groups.
- Can route based on:
  - **Host headers**
  - **Path patterns**
  - **Query strings**
  - **Source IP**
- Enables **consolidation**: multiple apps on **one ALB**.

---

## Application Load Balancer (ALB)

### Layer 7 (Application Layer) Load Balancer

- Protocols: **HTTP**, **HTTPS**
- Can inspect:
  - Content type
  - Cookies
  - Custom headers
  - User-agent
  - Application behavior

### Not Supported:

- Other Layer 7 protocols (e.g., SMTP, SSH, gaming protocols).
- Cannot listen to **TCP**, **UDP**, or **TLS**.

### SSL Termination:

- **All HTTPS traffic is terminated at the ALB**.
- Cannot perform **end-to-end SSL passthrough**.
- ALBs must have **SSL certificates** installed.
- New connection initiated from ALB to backend targets.

> ⚠️ Important for security-sensitive environments: if unbroken SSL is needed, ALB **cannot** be used.

### Performance:

- Slower than NLBs due to deeper protocol inspection.
- Not ideal for ultra-low latency needs.

### Health Checks:

- Supports **Layer 7 health checks** (e.g., application-specific URL checks).

---

## ALB Listener Rules

### Rule Types

- **Conditions**: Evaluate traffic characteristics
  - Host header
  - Path pattern
  - Query string
  - HTTP methods
  - Source IP
- **Actions**: What to do with matched traffic
  - Forward to target group
  - Redirect
  - Fixed response
  - Authenticate (e.g., OpenID, Cognito)

### Example:

- Rule: `source IP = 1.3.3.7` → forward to corporate-specific target group.
- Rule: `host = doggogram.io` → redirect to `catagram.io`.

> ALBs provide highly flexible routing at Layer 7.

---

## Network Load Balancer (NLB)

### Layer 4 (Transport Layer) Load Balancer

- Protocols: **TCP**, **UDP**, **TLS**
- No support for HTTP/HTTPS-level logic:
  - No visibility into headers or cookies
  - No session stickiness (at HTTP level)

### Performance:

- Extremely fast — **millions of requests per second**
- ~25% of ALB latency
- Ideal for:
  - Low-latency apps
  - Non-HTTP protocols (e.g., SMTP, SSH, gaming, financial apps)

### Health Checks:

- **Basic checks only**: ICMP or TCP handshake
- Cannot validate application-level health

### Benefits:

- **Static IP support** — good for **whitelisting**
- **Unbroken TCP passthrough** — supports **end-to-end encryption**
  - Can listen on TCP and forward without SSL termination

> ✅ Use NLB + TCP listener for unbroken encryption.

### Additional Use Case:

- **AWS PrivateLink** requires NLB

---

## Summary: When to Use ALB vs NLB

| Requirement                                         | Use |
| --------------------------------------------------- | --- |
| HTTP/HTTPS routing                                  | ALB |
| Advanced routing (path, host, header, query)        | ALB |
| Cookie/session-based routing                        | ALB |
| Authentication via Cognito or OpenID                | ALB |
| Non-HTTP protocols (SMTP, SSH, custom game traffic) | NLB |
| Static IP needed for whitelisting                   | NLB |
| End-to-end unbroken SSL/TLS                         | NLB |
| Best performance / lowest latency                   | NLB |
| Use with AWS PrivateLink                            | NLB |

> ✅ For most scenarios, **default to ALB** unless specific NLB use cases are required.

---

## Final Notes

- ALBs are more feature-rich but slightly slower.
- NLBs are faster and support lower-level networking use cases.
- Choose based on:
  - **Protocol**
  - **Security requirements**
  - **Performance needs**
  - **Integration needs** (e.g., PrivateLink)

---
