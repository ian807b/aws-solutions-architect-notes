# Elastic Load Balancer (ELB) Architecture — Part 2

## Multi-Tier Architecture Example

We’re continuing from Part 1 with a **typical multi-tiered application** structure:

- **VPC** with two Availability Zones.
- **Internet-facing Load Balancer** on the left.
- **Web Tier**: Auto Scaling Group of web instances.
- **Internal Load Balancer** (private IPs only) in the middle.
- **Application Tier**: Auto Scaling Group of application instances.
- **Database Tier**: A pair of Aurora DB instances.

This forms a classic **three-tier architecture**: Web → Application → Database.

---

## Why Use Load Balancers Between Tiers?

Without ELBs:
- Bob (the user) connects directly to a web instance.
- That web instance connects directly to a specific application instance.
- Tight coupling: failure or scaling in any tier disrupts the flow.

With ELBs:
- **Bob communicates only with an ELB node**, not directly with a web instance.
- Load balancer **abstracts away** infrastructure details.
- When a web instance communicates with the app tier, it does so through an **internal load balancer**.
- Result: tiers are **loosely coupled** and **scale independently**.

> ✅ ELBs abstract both incoming and inter-tier communication.

---

## Independent Scaling Example

- Application tier scales from 2 → 4 instances due to increased load.
- Web tier continues functioning **without reconfiguration**.
- Load balancer makes this seamless by abstracting backend changes.

---

## Cross-Zone Load Balancing

### Concept

- ELBs deploy **at least one node per AZ**.
- DNS resolves to **all nodes**, distributing traffic evenly.

Example:
- Two AZs → two ELB nodes → 50% of traffic to each node.

### Historical Limitation

Originally:
- Each ELB node could only route traffic to **instances in its own AZ**.

Problem:
- Uneven instance count across AZs caused **load imbalance**.

Example:
- AZ A: 4 EC2 instances
- AZ B: 1 EC2 instance
- Result: the one instance in AZ B could receive **50% of all traffic**.

### Cross-Zone Load Balancing: The Fix

- **Enables each ELB node to route to any instance** in any AZ.
- Provides **even traffic distribution**, regardless of where instances are.

> ✅ **Enabled by default** for Application Load Balancers (ALBs).
> ❗️ Important for the exam — often tested as the solution to uneven load distribution.

---

## Key Architectural Points: Recap

### 1. ELB Node Deployment
- One ELB node is deployed **per AZ/subnet**.
- You pick **one subnet per AZ**.
- A **DNS A record** is created to distribute incoming requests across all ELB nodes.

### 2. Auto Scaling
- ELB nodes **scale automatically** based on demand.

### 3. Cross-Zone Load Balancing
- ALBs have this **enabled by default**.
- Ensures **equal distribution** of traffic across all registered instances.

### 4. Internet-Facing vs. Internal ELBs

| Type            | IP Addressing | Notes                                               |
|-----------------|----------------|-----------------------------------------------------|
| Internet-Facing | Public + Private | Accepts traffic from public internet               |
| Internal        | Private only     | Used for intra-VPC communication (e.g., app tiers) |

- **Only difference is IP addressing**.
- Internet-facing ELB nodes can communicate with:
  - **Public EC2 instances**
  - **Private EC2 instances**

> ❗️ EC2 targets **do not need public IPs**, even with internet-facing ELBs.

### 5. Listener Configuration
- Determines which **protocols and ports** the ELB listens to.
- Will be covered in more depth in a future lesson.

### 6. Subnet Size Requirements

- ELBs need **8+ free IP addresses** per subnet.
- AWS recommends using a **/27 subnet** (32 IPs, 27 usable).
- Technically, a **/28 (11 usable)** is sufficient, but doesn’t allow much room for scaling.

> ✅ **Use /27** as the safe answer for subnet sizing in most exam questions.

---

## Conclusion

You now understand:
- Why ELBs are critical in multi-tiered architectures
- How they **decouple and abstract** tiers
- What **cross-zone load balancing** is and why it matters
- The **architectural fundamentals** for ELB deployment

When you're ready, continue to the next lesson.
