# Custom VPCs Part I (Theory)

![VPC1](/24_Diagrams/VPCStucture-1.png)

---

## Goal: A Multi-Tier Custom VPC

> ### Consider US Region 1 (`10.16.0.0/16`).

### VPC Structure

- **CIDR Block**: `10.16.0.0/16`
- **Tiers** (4 total):
  - Reserved
  - Database
  - Application (App)
  - Web
- **Availability Zones**: A, B, C (leave capacity for 1 more)
- **Subnets**:
  - 4 tiers × 4 AZs = 16 possible
  - Implementing 4 tiers × 3 AZs = 12 subnets for now

### Additional Components

- Internet Gateway (IGW)
- NAT Gateways
- Bastion Host (Bad Practice)
- Network ACLs (NACLs)
- Data Transfer Cost Analysis

---

## Essential VPC Architecture Concepts

### VPC Fundamentals

- **Regionally Isolated**: VPC spans all AZs in one region
- **Logically Isolated Network**: Even in one AWS account
- **Boundary Control**: No traffic in/out without explicit configuration
- **Blast Radius Contained**: Compartmentalized impact
- **Customizable**: From simple to complex architecture
- **Hybrid Capable**: Can connect to other cloud or on-prem

---

### VPC Tenancy

- **Default (Recommended)**:
  - Shared hardware (with other customers)
  - Can choose dedicated for individual resources later

- **Dedicated**:
  - All resources run on dedicated hardware
  - Set at VPC creation, **non-reversible**
  - Comes at a **cost premium**

---

## IP Addressing: IPv4

- **Private CIDR Block**:
  - Mandatory upon VPC creation
  - Smallest: `/28` (16 IPs)
  - Largest: `/16` (65,536 IPs)

- **Secondary IPv4 CIDRs**:
  - Add up to 5 (default limit; increase via support)

- **Public IPs**:
  - Used for public internet access
  - Not assigned by default; must be configured

---

## IP Addressing: IPv6 (Optional)

- **IPv6 /56 block** can be assigned to a VPC
- **AWS-allocated or BYO IPv6 block**
- **No private/public concept**: All are **publicly routable**
- **Still need to configure routing + access control**

Use **Egress-Only Internet Gateway** for outbound control  
Use **IGW** for inbound public access

**Recommendation**: Consider enabling IPv6 by default for future readiness

---

## DNS in VPC (Route 53)

AWS provides built-in DNS via Route 53 in every VPC.

- Built-in DNS means that AWS automatically provides a private DNS resolver service within a VPC.
- Thus, no need to set up DNS servers for basic name resolution within the VPC.
- For example, an EC2 instance gets a private DNS: `ip-10-0-0-10.ec2.internal`.
- Other instances in the same VPC can use this hostname (ip-10-0-0-10.ec2.internal) to reach the EC2 instance, instead of remembering or knowing its IP.

- **DNS IP**: Base of VPC + 2  
  E.g., VPC: `10.0.0.0/16` → DNS: `10.0.0.2`
  I.e., Every resource created within the VPC will use `10.0.0.2` as its DNS resolver for all domain name inquiries.

### Two Key DNS Settings

1. **Enable DNS Hostnames**
   - Assigns public DNS names to instances with public IPs
   - (`true` or `false`)

2. **Enable DNS Support**
   - Enables VPC-level DNS resolution (via +2 IP)
   - (`enabled` or `disabled`)

**Troubleshooting Tip**:  
If you encounter DNS resolution issues, **check these two options first**.

---