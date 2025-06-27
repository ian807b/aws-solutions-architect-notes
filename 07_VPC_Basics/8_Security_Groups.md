# AWS Security Groups

## Overview

Security Groups are one of the two primary security filtering mechanisms in AWS. The other is **Network Access Control Lists (NACLs)**.

While both serve similar high-level purposes—controlling network traffic—they operate differently and understanding these differences is critical for both the **AWS Solutions Architect Certification Exam** and real-world implementations.

---

## Stateless vs. Stateful

| Feature              | Security Groups          | NACLs                   |
|----------------------|---------------------------|--------------------------|
| Nature               | Stateful                  | Stateless                |
| Response Tracking    | Automatically tracked     | Requires explicit rules  |
| Explicit Deny        | ❌ Not supported (implicit deny only) | ✅ Supported     |
| Primary Use          | Allow trusted traffic     | Block specific actors    |

Security Groups are **stateful**. This means that if you allow a request (inbound or outbound), the response is automatically allowed.

> SGs can only allow traffic.

### Example:
If a web server listens on TCP port `443`, and you allow inbound traffic on that port, response traffic through **ephemeral ports** is automatically permitted—no need to configure these manually.

---

## Implicit vs. Explicit Deny

Security Groups **do not support explicit deny rules**. If you do not allow something explicitly, it is implicitly denied.

**Important:**  
You cannot use security groups to block specific IP addresses. For example, if `0.0.0.0/0` is allowed on TCP port `443`, you cannot block a specific malicious actor's IP within that range using security groups.

To block known bad actors, combine Security Groups with **NACLs**, which do support **explicit deny**.

---

## OSI Layer and Logical Referencing

Security Groups operate at a higher level in the **OSI 7-layer stack** than NACLs, and provide more advanced functionality:
- Allow IP and **CIDR-based rules**
- Support **referencing logical AWS resources**, including:
  - Other Security Groups
  - Themselves (self-referencing)

---

## Attachment to Network Interfaces (ENIs)

Security Groups are **not** attached directly to instances or subnets.

Instead, they are attached to **Elastic Network Interfaces (ENIs)**. When you assign a security group to an EC2 instance, AWS actually attaches it to the instance's **primary ENI**.

> 🔹 **Exam Tip:** Security groups attach to ENIs, not instances.

---

## Visual Example: Public Subnet + EC2

Imagine an EC2 instance in a public subnet:

- Instance has a **primary ENI**
- A user (Bob) accesses the instance via **HTTPS (TCP port 443)**

The Security Group:
- Allows **inbound TCP 443 from any IP**
- Automatically allows **response traffic** back to Bob due to stateful nature

> 🔸 Again: **You cannot explicitly block Bob** via Security Group if he is a bad actor. Use a NACL for that.

---

## Advanced Feature: Logical References

### Setup:
- **Public Web Subnet** with a "web" instance
- **Private App Subnet** with a "backend" instance
- Two Security Groups: `a4l-web` and `a4l-app`

### Requirements:
- Bob connects to the web instance via TCP `443`
- Web instance connects to the app instance via TCP `1337`

### Solution Options:
1. Allow web instance IP in app SG — not scalable.
2. Allow subnet CIDRs — better, but still static.
3. **✅ Best Practice: Use Security Group References**

### Example:
The `a4l-app` security group allows **TCP 1337 inbound** **from `a4l-web` SG**.

This means:
- Any instance with `a4l-web` SG can connect to instances with `a4l-app` SG on port `1337`
- No need to manage IPs or CIDRs
- Auto-scales with instances joining the respective SGs

---

## Self-Referencing Security Groups

Useful for:
- Auto Scaling Groups
- Internal app communication
- Clustered services (e.g., Microsoft Domain Controllers)

### Example:

Security Group for backend app:
- Allows TCP `1337` from `a4l-web` SG
- Also allows **all traffic from itself**

Effect:
- Instances with this SG can talk to each other
- Automatically handles IP changes
- Simplifies intra-app communication

---

## Summary

| Security Groups                                 | Network ACLs                        |
|-------------------------------------------------|-------------------------------------|
| Stateful                                         | Stateless                           |
| No explicit deny (implicit deny only)           | Can explicitly deny traffic         |
| Attached to ENIs                                | Attached to Subnets                 |
| Allow logical resource referencing              | No logical references               |
| Use for allowing trusted traffic                | Use for blocking specific threats   |

- Use Security Groups to **allow** traffic
- Use NACLs to **block** malicious traffic
- Combine both for defense-in-depth

---
