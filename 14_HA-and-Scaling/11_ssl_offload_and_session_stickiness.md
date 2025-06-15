# SSL Offload & Session Stickiness

## SSL Handling Methods

There are **three SSL handling architectures** used by ELBs:

### 1. **SSL Bridging (Default for ALB)**

- Client → HTTPS → **Application Load Balancer**
  - SSL is **terminated** (decrypted) at the ALB.
- ALB → HTTPS → EC2 (new encrypted connection)
  - Re-encrypted for secure transfer to backend.

**Requirements:**

- SSL certificate installed on both:
  - ALB
  - EC2 instances

**Pros:**

- ALB can **see HTTP contents** (needed for Layer 7 routing).
- Supports ALB features (host/path-based routing, header inspection, etc.).

**Cons:**

- Certificates must be managed on both ALB and EC2s.
- Backend EC2s must **perform cryptographic operations** (resource-intensive).
- Security-sensitive orgs may be concerned about AWS having access to certs.

---

### 2. **SSL Passthrough (Used with NLB)**

- Client → HTTPS → **Network Load Balancer (TCP)**
  - No decryption by NLB; traffic is passed as-is to backend.
- Backend EC2 → decrypts traffic using its own certificate.

**Requirements:**

- SSL certificate **only on EC2**
- NLB listener configured for **TCP**

**Pros:**

- Complete **end-to-end encryption**
- AWS never accesses SSL certs
- Can integrate with **CloudHSM** for increased security

**Cons:**

- NLB cannot inspect or route based on HTTP content (Layer 7 features unavailable)
- Backend EC2s must decrypt traffic (adds CPU load)
- No ALB-style advanced routing

---

### 3. **SSL Offload**

- Client → HTTPS → **Load Balancer**
  - SSL is terminated on the Load Balancer
- LB → HTTP → EC2
  - Traffic between LB and EC2 is **unencrypted**

**Requirements:**

- SSL certificate **only on Load Balancer**

**Pros:**

- Backend EC2s do **not** perform cryptographic operations
- Reduced EC2 instance load → cost savings
- No need to manage certificates on instances

**Cons:**

- Traffic from LB to EC2 is **unencrypted** (insecure over internal AWS network)
- Not suitable for sensitive workloads unless additional encryption is added

---

## Session Stickiness (Session Affinity)

**Definition**: Stickiness ensures that a client continues to be routed to the **same backend instance** during a session.

### Behavior without Stickiness

- ELB distributes requests **evenly** across backend EC2s.
- If user session data is stored **locally** on an EC2 instance:
  - User may be disconnected or forced to re-login
  - Shopping carts, session info, etc., may be lost

### Use Case for Stickiness

- Apps that **store state locally** on the EC2 instance
- Enables temporary workaround for non-stateless applications

---

### How ALB Stickiness Works

- Enabled **per target group** on the ALB
- ELB issues a cookie named `AWSALB`
- Cookie duration: **1 second to 7 days**
- For the life of the cookie, the user is routed to the **same EC2 instance**

### Ends When:

- **Instance fails** → user is routed to another instance
- **Cookie expires** → new cookie assigned

---

### Drawbacks of Stickiness

- Can cause **uneven traffic distribution**
  - Heavy users all routed to the same EC2
- Reduces effectiveness of **load balancing**
- Not suitable for **highly scalable or stateless** architectures

---

### Best Practice

> **Design stateless applications**

- Store session data in external stores:
  - **Amazon DynamoDB**
  - **Amazon ElastiCache**
  - **Amazon RDS**

Allows true load balancing with **no need for stickiness**.

---
