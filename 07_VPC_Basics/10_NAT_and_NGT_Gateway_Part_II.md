# NAT and NAT Gateway Part II

## NAT Gateway Resiliency

![NAT2](/24_Diagrams/NAT-2.png)

---

To achieve high availability across AZs:

- Deploy **one NAT Gateway per AZ** (e.g., AZA, AZB, AZC)
- Each AZ’s **private subnets** must use their **own route table**
- Each private route table must:
  - Include a **default IPv4 route (`0.0.0.0/0`)**
  - Point to the NAT Gateway **in the same AZ**

> This design ensures that **if an AZ fails**, other AZs continue to function without disruption.

---

## Important Exam Note

> NAT Gateways are **only AZ-resilient**

- They **can scale and recover** within an AZ
- But **will fail** if the entire AZ goes down
- You must **provision NAT Gateways per AZ** for full regional availability

---

![NAT3](/24_Diagrams/NAT-3.png)

---

## Legacy Option: NAT Instances

NAT instances are older, EC2-based solutions for NAT.

- NAT is implemented on an **EC2 instance**
- Not recommended today, but **still appears on the exam**

### Source/Destination Check

By default, EC2 instances **drop packets** where:

- The instance is **neither the source nor destination**

To allow NAT instance functionality:

- Disable **Source/Destination Checks**
- Can be done via Console, CLI, or API

> Found under EC2 instance settings (right-click > Disable source/dest check)

---

## NAT Gateway vs NAT Instance

| Feature                 | NAT Gateway               | NAT Instance                   |
| ----------------------- | ------------------------- | ------------------------------ |
| Type                    | Managed Service           | Self-managed EC2 instance      |
| Availability            | AZ-resilient              | Fails with EC2 or AZ           |
| Performance             | Scales to 45 Gbps         | Limited to EC2 instance type   |
| Maintenance             | None (fully managed)      | Manual (OS, patching, scaling) |
| Elastic IP Required     | ✅                        | ✅                             |
| Port Forwarding         | ❌ Not supported          | ✅ Supported                   |
| Can act as Bastion Host | ❌ No                     | ✅ Yes                         |
| Security Groups         | ❌ Not supported          | ✅ Supported                   |
| Cost                    | Charged per hour + per GB | Can be free tier / fixed cost  |
| IPv6 Support            | ❌ Not supported          | ✅ (but NAT not needed)        |

> **Exam Tip**: NAT Gateway **cannot**:
>
> - Be used as a **bastion host**
> - Perform **port forwarding**
> - Use **security groups**

---

## Cost Considerations

- **NAT Gateway Charges**:

  - **Hourly Rate** (~$0.045/hr)
  - **Data Transfer Rate** (~$0.045/GB)

- **NAT Instance**:
  - Can be **cheaper** for:
    - Low traffic volumes
    - Free tier instances
    - Fixed-cost scenarios

---

## Special Use Cases for NAT Instances

- Can act as:
  - **Bastion Host**
  - **Port Forwarder**
  - **Multi-purpose server**
- Allow **SSH access and control**
- Support **security groups and NACLs**

---

## NAT and IPv6: Key Facts

> **NAT is not used with IPv6**

- **IPv6 addresses are publicly routable**
- **Internet Gateway** works directly with IPv6 traffic
- **NAT Gateways do not support IPv6**

### To enable IPv6 internet access:

- Use default IPv6 route: `::/0`
- Point it to the **Internet Gateway**

> If this route exists and no NACL or SG blocks it, the instance has **full IPv6 connectivity**

---

## Egress-Only Internet Gateway

- Specialized for **IPv6**
- Allows **outbound-only** access for IPv6-enabled instances
- Blocks **inbound connections** from the public internet
- We'll cover this in detail later

---

## Summary

- **Use NAT Gateways** for:
  - Simplicity
  - Performance
  - Availability
- **Use NAT Instances** for:
  - Cost sensitivity
  - Port forwarding / multi-purpose roles
- **Never use NAT Gateways with IPv6**

---
