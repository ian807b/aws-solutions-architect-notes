# VPC Sizing and Structure Part I

## VPC & IP Planning

When designing a VPC, you must consider:

### 1. **VPC CIDR Block**
- One or more CIDR blocks can be assigned
- **Changing later is difficult**

#### Review - CIDR Block? [Foundational Knowledge](https://github.com/ian807b/aws-solutions-architect-notes/blob/main/0_Foundational_Knowledge/0_AWS_SAA_Foundational_Network_Knowledge.pdf)
- **CIDR (Classless Inter-Domain Routing)** defines a **range of IP addresses** using a notation like `10.0.0.0/16`
- The `/16` means 16 bits are reserved for the network, giving **65,536 total IPs**
- Helps determine how large your VPC network can be

### 2. **VPC Size**
- Dictates how many IPs (resources) you can host
- Each service (EC2, RDS, etc.) consumes IPs

### 3. **Avoid Overlapping IPs**
- Prevent routing issues with:
  - Other VPCs
  - On-prem networks
  - Other cloud environments (e.g., Azure, GCP)
  - Partner/vendor networks

### 4. **Think Long-Term**
- Design with future expansion in mind

### 5. **Network Structure (Tiering)**
- Web tier, App tier, DB tier, etc.
- Each should be segmented (e.g., a subnet for each tier, possibly in more than 1 AZs)

---

## Scenario: animals4life (a4l) Organization


![VPC1](/24_Diagrams/VPC1.png)

---

### Organization Structure:
- **Major Offices:** London, New York, Seattle
- **Existing Networks:**
  - `192.168.10.0/24` → On-prem (Brisbane) (192.168.10.0 - 192.168.10.255)
  - `10.0.0.0/16` → AWS pilot project (10.0.0.0 - 10.0.255.255)
  - `172.31.0.0/16` → Azure pilot (also AWS default VPC range) (172.31.0.0 - 172.31.255.255)
- **Office IPs:**
  - `192.168.15.0/24` → London (192.168.15.0 - 192.168.15.255)
  - `192.168.20.0/24` → New York (192.168.20.0 - 192.168.20.255)
  - `192.168.25.0/24` → Seattle (192.168.25.0 - 192.168.25.255)
- **GCP default range:**
  - `10.128.0.0/9` → Huge block (10.128.0.0 – 10.255.255.255)

> These are all **reserved** and **must not** be reused in the new VPC designs.

---

## CIDR Block and Subnetting Review

- IP address = start of a range
- Prefix (`/x`) = how many fixed bits
  - `/8` → 1st octet fixed → 10.0.0.0 – 10.255.255.255
  - `/9` → half of `/8` → 10.0.0.0 – 10.127.255.255, 10.128.0.0 – 10.255.255.255

> Sample Calculation `192.168.10.0/24`
  - /24 is the CIDR suffix (or subnet mask)
  - First 24 bits of the 32-bit IPv4 address are used for the network portion 
  - The remaining for the host portion (usable IPs)
  - Host bits: 32 - 24 = 8
  - The number of possible unique addresses is 2^8 = 256 total IP addresses
  - Thus, `192.168.10/24` spans 192.168.10.0 - 192.168.10.255, which includes the reserved ones

> A CIDR block defines the total IP addresses range the VPC can use.

---

## AWS VPC Sizing Limits

- **Min size** = `/28` (16 IPs)
- **Max size** = `/16` (65,536 IPs)

---

## Adrian's Preferences & Suggestions

- Use **10.x.y.z** private IP range
- Avoid common ranges:
  - `10.0.x.x` (most common)
  - `10.1.x.x` (second most common)
  - Also avoid `10.2` to `10.10` to be extra safe
- Start with **`10.16.0.0/16`**

> Base-2 aligned (`10.16`, `10.32`, etc.) makes subnetting and planning cleaner

---

## Calculating Needed IP Ranges

We plan for:
- **5 Regions:** (3 US, 1 Europe, 1 Australia)
- **2 VPCs per region**
- **4 AWS Accounts**

### Calculation:

- 5 regions × 2 VPCs = 10 ranges
- 10 ranges × 4 accounts = 40 total ranges

> Each VPC and must be assigned a unique CIDR block, even though they're isolated by default.

---

## Final Usable Range

- Avoid:
  - `10.0.0.0 – 10.10.255.255`
  - `10.128.0.0 – 10.255.255.255` (Google Cloud)
- Usable range:
  - **`10.16.0.0 – 10.127.255.255`** (Safe, ~112 /16 blocks)

---

## Summary

| Design Principle                  | Consideration                                                                 |
|----------------------------------|------------------------------------------------------------------------------|
| Choose VPC CIDR carefully        | Hard to change later                                                        |
| Avoid IP overlap                 | On-prem, other clouds, AWS defaults, partners                               |
| Use private, uncommon ranges     | `10.16.0.0/16` is a good base                                                |
| Plan for future scale            | Estimate based on max regions and accounts                                  |
| Use tiers & AZs for structure    | Enables security segmentation and resilience                                |

---
