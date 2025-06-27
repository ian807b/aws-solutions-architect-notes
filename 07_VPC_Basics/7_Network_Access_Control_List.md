# Network Access Control Lists (NACLs)

**Network Access Control Lists (NACLs)** is a **stateless firewall** mechanism.

---

## What Are NACLs?

- **Traditional-style firewall** for VPCs
- **Associated with subnets**, not individual instances
- Filter **inbound and outbound traffic** as it **crosses the subnet boundary**
- Traffic between **instances within the same subnet** is **not affected**

---

## Stateless Rules

- NACLs are **stateless**, so:
  - **Request and response are treated independently**
  - You must create **two rules per communication** (one for each direction)
- Contains:
  - **Inbound rules**: Affect traffic entering the subnet
  - **Outbound rules**: Affect traffic leaving the subnet

---

## ALLOW & DENY Rules

- Each rule can **explicitly allow** or **explicitly deny** traffic
- Rules are processed in **ascending order of rule numbers**
- **First match wins**; remaining rules are ignored
- If no rule matches, **default implicit deny** (`*` rule) is applied

---

## Example: Simple Web Access

### Scenario:
- Bob's laptop makes an HTTPS request (TCP 443) to a web server in a subnet
- Web server sends back response over **ephemeral port**

### Required NACL Rules:

#### Inbound (Web Subnet):
- Allow: `0.0.0.0/0` on TCP port `443` (for request from Bob)

#### Outbound (Web Subnet):
- Allow: `0.0.0.0/0` on TCP ports `1024-65535` (ephemeral ports for response)

> Not very secure, but necessary for stateless firewall functionality

---

## Multi-Tier Example


![NACL](/24_Diagrams/NACL.png)

---

### Architecture:
- Bob → Web Server → App Server  
- Involves **cross-subnet communication**

### Rule Requirements:

#### Bob → Web Server
- Inbound (Web subnet): allow 443
- Outbound (Web subnet): allow ephemeral port

#### Web Server → App Server
- Outbound (Web subnet): allow app port (e.g., TCP 8080)
- Inbound (App subnet): allow app port

#### App Server → Web Server (response)
- Outbound (App subnet): allow ephemeral port
- Inbound (Web subnet): allow ephemeral port

> Every **subnet boundary** crossed means a new set of NACL rules

---

## Complexity in Real Deployments

- Each connection crossing subnets may require:
  - 2 rules per NACL × 2 subnets = **4 rules per connection**
- Software updates and NAT may require even more rules

---

## Default vs Custom NACLs

### Default NACL (attached to new VPCs by default)
- **Implicit deny** plus **explicit allow all**
- Effectively allows **all traffic**
- **Beginner-friendly**, low admin overhead

### Custom NACL
- Initially has **only deny rules**
- If associated with a subnet: **all traffic is denied**
- Requires **manual configuration** of rules

---

## Key Points for Exams and Real Usage

- **Stateless**: Must define both request and response rules
- **Subnet-scoped**: NACLs only apply to traffic crossing **subnet boundaries**
- **No resource-level awareness**: Can only use **IPs, ports, and protocols**
- **Can explicitly deny traffic**: Security groups can’t do this
- **Good for blocking bad actors** via IP or IP range
- Often **used alongside security groups**:
  - SGs = allow known good
  - NACLs = block known bad

---
