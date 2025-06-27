# NAT and NAT Gateway Part I

## What is NAT?

**NAT (Network Address Translation)** is a process that modifies IP packet headers by changing the **source** or **destination** IP addresses. In AWS, NAT allows **private resources to initiate outbound internet access**, while preventing **unsolicited inbound access**.

---

## Static NAT via Internet Gateway

- **Private IP → Public IP**: When a packet leaves a resource with a public IPv4 address, the IGW changes the **source IP** to the **public IP**.
- **Response**: When packets return, the IGW changes the **destination IP** from the public IP back to the original private IP.

---

## NAT vs. IP Masquerading

What most people mean by “NAT” is actually a specific technique called **IP Masquerading**, which:

- Hides multiple private IPs behind **a single public IP**.
- Allows **many-to-one** translation.
- Is **outbound only** — the private instances can initiate connections, but cannot receive them.

> **Key Limitation**: NAT does not allow **incoming** connections initiated from the internet.

---

## NAT in AWS

### Two NAT Options

1. **NAT Instance** (legacy option, user-managed EC2 instance)
2. **NAT Gateway** (managed AWS service)

---

## Architecture Example

![NAT-1](/24_Diagrams/NAT-1.png)

---

### VPC Setup:

- **Private Subnet**:  
  `10.16.32.0/20`  
  Instances:
  - `i-01`: `10.16.32.10`
  - `i-02`: `10.16.32.20`
  - `i-03`: `10.16.32.30`

These IPs are **private** and not routable over the public internet.

### Problem:

How can these private instances download updates (e.g., from a public software repo)?

### Solutions:

- Make the subnet public? ❌ Not ideal for multi-tier security.
- Host update servers inside VPC? ✅ Possible, but **adds complexity**.
- Use a **NAT Gateway** ✅

---

## How NAT Gateway Works

1. **Deployed in a public subnet** (so it gets a **public IP**).
2. **Public subnet** route table:
   - Default IPv4 route: points to the **Internet Gateway**
3. **Private subnet** route table:
   - Default IPv4 route: points to the **NAT Gateway**

---

### Packet Flow Example

1. Instance `i-01` sends a packet to a software update server (`1.3.3.7`)
2. Packet has:
   - Source IP: `10.16.32.10`
   - Destination: `1.3.3.7`
3. Packet hits the private subnet route table → routed to **NAT Gateway**
4. NAT Gateway:
   - Records the connection in a **translation table**
   - Changes **source IP** to **its own internal IP**
5. NAT Gateway forwards packet to **Internet Gateway**
6. IGW modifies **source IP** to NAT Gateway's **public IP**
7. Response flows back through the same path:
   - IGW → NAT Gateway → original private instance

---

## Key Concepts Recap

| Functionality                | Responsible Component  |
| ---------------------------- | ---------------------- |
| Public IP allocation         | Internet Gateway       |
| Many-to-one IP translation   | NAT Gateway            |
| Outbound access for privates | NAT + Internet Gateway |

---

## NAT Gateway Deployment Requirements

- Must be placed in a **public subnet**
- Requires:
  - **Internet Gateway** (attached to VPC)
  - **Route table** with default route (`0.0.0.0/0`) to **IGW**
  - **Elastic IP**: static public IP allocated to your AWS account

> 🔹 Elastic IPs will be covered in more detail later.

---

## Availability Considerations

- **AZ resilient**, but **not region-wide resilient**
- To be resilient across multiple AZs:
  - Deploy **one NAT Gateway per AZ**
  - Configure each **private subnet route table** to point to the NAT Gateway **in the same AZ**

---

## Performance & Scaling

- Managed by AWS
- **Scales up to 45 Gbps**
- Can deploy **multiple NAT Gateways** to:
  - Increase bandwidth
  - Distribute traffic across subnets

---

## Cost Structure

NAT Gateways incur:d

1. **Hourly Cost** (e.g., ~$0.045/hour per gateway)
2. **Data Processing Cost** (e.g., ~$0.045/GB)

> Costs vary by region and are charged in full-hour increments.

---

## Summary

- NAT enables **outbound-only internet access** for private subnets.
- Requires both:
  - **NAT Gateway** (for many-to-one translation)
  - **Internet Gateway** (to route to the public internet)
- NAT Gateway:
  - Needs to be in a **public subnet**
  - Uses **Elastic IP**
  - Is **AZ-resilient**, not region-resilient
- **For HA**: Deploy one NAT Gateway per AZ

---
