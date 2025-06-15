# Auto Scaling Group (ASG) Scaling Policies

## Do You Need a Scaling Policy?

- **No** — ASGs **can be created without scaling policies**.
- In such cases, the ASG operates with:
  - **Static min, max, and desired capacity**.
- **Manual Scaling** = Manually adjusting these values.
  - Useful for:
    - Testing
    - Emergencies
    - Fixed capacity requirements (e.g., cost control)

---

## Types of ASG Scaling

### 1. **Simple Scaling**
- Uses **CloudWatch alarms** to trigger actions:
  - E.g., add 1 instance if CPU > 40%
  - E.g., remove 1 instance if CPU < 40%
- **Static adjustment** regardless of how far the metric deviates from the threshold.
- **Inflexible**: Always adds/removes the same amount.

---

### 2. **Step Scaling**
- Also triggered by **CloudWatch alarms**.
- Uses **step adjustments** based on how far the metric deviates.
- Example:
  - CPU 50–59% → do nothing
  - CPU 60–69% → add 1 instance
  - CPU 70–79% → add 2 instances
  - CPU 80–100% → add 3 instances
  - And in reverse for scale-in
- **More efficient** than simple scaling for handling variable load.

---

### 3. **Target Tracking Scaling**
- Automatically maintains a target metric (e.g., 50% CPU).
- Built-in metrics:
  - CPU utilization
  - Average network in/out
  - ALB request count per target
- **Automatically calculates** how many instances to add or remove.
- Scaling aggressiveness depends on how far the metric is from the target.

---

### 4. **SQS-Based Scaling**
- Common for **queue-processing architectures**.
- ASG scales based on the **approximate number of visible messages** in the queue.
- As queue length grows → scale out.
- As queue is processed → scale in.

---

## Deep Dive: Simple Scaling vs. Step Scaling

### Simple Scaling
- Example policy:
  - If CPU > 50% → add 2 instances
  - If CPU < 50% → remove 2 instances
- Problems:
  - Static adjustment regardless of deviation.
  - CPU at 51% and 99% both result in the same action.
  - Inefficient for handling highly variable traffic.

### Step Scaling
- More flexible and **context-aware**.
- Can define multiple steps per alarm threshold.
- Example scale-out:
  - 50–59% → do nothing
  - 60–69% → add 1
  - 70–79% → add 2
  - 80–100% → add 3
- Example scale-in:
  - 40–49% → do nothing
  - 30–39% → remove 1
  - 20–29% → remove 2
  - 0–19%  → remove 3
- **Best for handling variable or bursty traffic patterns.**

---

## Scaling Example Walkthrough

### Initial State:
- Min: 1, Max: 4, Desired: 1
- CPU = 10%

### Scenario with Simple Scaling:
- CPU jumps to 60% → add 2 instances (desired = 3)
- CPU drops to 10% → remove 2 instances (desired = 1)
- Same scaling behavior regardless of CPU being 51% or 100%

### Scenario with Step Scaling:
- CPU = 5% → attempt to remove 3 → ASG remains at min = 1
- CPU = 100% → add 3 → ASG reaches max = 4
- CPU drops to 55% → within no-action range → no change
- CPU = 5% → remove 3 → drop to desired = 1 (min)
- CPU = 60% → add 1 → desired = 2

> Step scaling allows **granular control** over scaling, matching response to load intensity.

---

## Key Takeaways

- **Manual scaling** is always possible but not automatic.
- **Simple scaling** is easy but inefficient for fluctuating workloads.
- **Step scaling** is AWS-recommended for dynamic responsiveness.
- **Target tracking** is ideal for keeping metrics at specific thresholds.
- **SQS-based scaling** is essential for decoupled, event-driven architectures.
- **Minimum and maximum size constraints** still apply to all scaling types.

> For the exam, know:
> - Step scaling vs. simple scaling differences.
> - Target tracking uses predefined metrics.
> - SQS queue length can drive ASG scaling.
> - Minimum/maximum capacity still governs limits.

---