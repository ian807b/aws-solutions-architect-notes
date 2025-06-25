# AWS VPC Subnets

![VPCSkeleton](/24_Diagrams/VPCSkeleton.png)

---

## Starting Architecture

- During the demo lesson, the diagram above is what we created (**skeleton VPC**).
- Later, we'll be **adding subnets** to create a **multi-tier, multi-AZ structure** (as shown in the diagram below).

> Note: In AWS diagrams,  
> - **Green** = Public subnet  
> - **Blue** = Private subnet  
> At this stage, all created subnets are **private** by default.

---

![VPCArch](/24_Diagrams/VPCStucture-1.png)

---

## What Is a Subnet?

- A **subnet** is a **sub-network** within a VPC.
- It exists **in one Availability Zone (AZ)** and **cannot span multiple AZs**.
- **Highly available architectures** use **multiple subnets in different AZs**.

### Key Facts:

| Concept                                | Explanation                                                                 |
|----------------------------------------|-----------------------------------------------------------------------------|
| One Subnet = One AZ                    | Subnet must be created in a single AZ and is fixed.                         |
| One AZ = 0 or more Subnets             | You can have many subnets in a single AZ.                                   |
| Communication                          | All subnets in a VPC can **communicate with each other** by default.        |
| IP Version Support                     | IPv4 required; IPv6 optional (if enabled on the VPC).                        |

---

## Subnet CIDR Ranges

- Each subnet gets a **non-overlapping** CIDR block within the VPC range.
- For IPv6, the subnet gets a **/64 block**, which is a subset of the VPC’s **/56 block**.

---

## Reserved IP Addresses (cannot use them)

Each subnet reserves **5 IP addresses**:

| Reserved IP       | Description                                                                 |
|-------------------|-----------------------------------------------------------------------------|
| Network Address   | First IP (e.g., `10.16.16.0`) - identifies the subnet itself.               |
| Network + 1       | Used by the **VPC router**.                                                 |
| Network + 2       | Used by **DNS** (e.g., `10.16.16.2`).                                       |
| Network + 3       | Reserved for **future AWS use**.                                            |
| Broadcast Address | Last IP (e.g., `10.16.31.255`) - reserved even though VPCs don't support broadcast. |

**Effective Usable IPs** = Total IPs - 5

> Example:  
> A `/20` subnet has **4096 IPs** → **4091 usable IPs**

---

## Dynamic Host Configuration Protocol (DHCP): A protocol that automatically assigns IPs

Each VPC has **one active DHCP option set** that defines:

- DNS servers
- NTP servers
- NetBIOS servers

### Key Notes:

- You **can’t edit** a DHCP option set.
- You **must create a new one** to change settings.
- The option set applies to **all subnets** in the VPC.

---

## Subnet-Level Configuration Options

### 1. Auto-Assign Public IPv4

- Determines whether instances in this subnet get a **public IP** on launch.
- This setting is essential to make a subnet **public**.

### 2. IPv6 Assignment

- Determines whether instances receive an IPv6 address.
- Requires both the **VPC and the subnet** to have IPv6 ranges.

---
