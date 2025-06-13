# Auto Scaling Group (ASG) Health Checks EC2 vs. ELB

## Purpose of Health Checks

- **Automatically detect and replace** unhealthy instances within an ASG.
- Ensures resilience and **self-healing** infrastructure.

---

## Types of Health Checks

There are **three** types of health checks supported by ASGs:

### 1. **EC2 Health Checks** (Default)
- ASG checks EC2 instance status.
- Considered **unhealthy** if the instance is:
  - `stopping`, `stopped`, `shutting-down`, `terminated`
  - **Impaired** (fails 2/2 EC2 status checks)
- Simple, but **not application aware**.

---

### 2. **ELB Health Checks**
- Used when ASG is attached to an **Elastic Load Balancer** (e.g., ALB).
- Instance must be:
  - In `running` state **and**
  - **Passing the ELB health check**
- ELB (esp. ALB) checks can be:
  - **Application-aware**
  - Configured for:
    - Specific URL paths
    - Expected HTTP response codes
    - **Text pattern matching**

> Best choice when you need to ensure the actual **application** is working, not just the EC2 instance.

---

### 3. **Custom Health Checks**
- External systems or scripts mark instances as healthy or unhealthy.
- Provides **customized** health status evaluation.
- Useful for:
  - Complex bootstrapping logic
  - External monitoring tools

---

## Health Check Grace Period

### What It Is:
- **Delay before health checks are enforced** on newly launched instances.
- Default: **300 seconds (5 minutes)**

### Why It Matters:
- Allows time for:
  - EC2 instance startup
  - Bootstrapping
  - Application initialization
- Prevents false negatives (premature failures)
  
> If the grace period is too short, the instance may be marked unhealthy **before fully initializing**, leading to a **continuous launch/terminate loop**.

---

## Summary Table

| Type                | Description                                           | Application Aware | Customizable |
|---------------------|-------------------------------------------------------|--------------------|---------------|
| EC2 (Default)       | Based on EC2 status checks                            | ❌                 | ❌            |
| ELB                 | Combines EC2 + Load Balancer health check             | ✅ (if ALB)         | ✅            |
| Custom              | External process determines health                    | ✅                 | ✅            |

---
