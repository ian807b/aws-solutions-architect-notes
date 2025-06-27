# VPC Sizing and Structure Part II

> Part I is for design of network in general. Part II is about the actual process of desgning the network for a4l.

---

## AWS-Suggested VPC Sizes

| VPC Size    | Netmask | Subnet Size | Hosts/Subnet        | Subnets/VPC | Total IPs                |
| ----------- | ------- | ----------- | ------------------- | ----------- | ------------------------ |
| Micro       | /24     | /27         | $2^5 - 5 = 27$      | $2^3 = 8$   | $27 \times 8 = 216$      |
| Small       | /21     | /24         | $2^8 - 5 = 251$     | $2^3 = 8$   | $251 \times 8 = 2008$    |
| Medium      | /19     | /22         | $2^{10} - 5 = 1019$ | $2^3 = 8$   | $1019 \times 8 = 8152$   |
| Large       | /18     | /21         | $2^{11} - 5 = 2043$ | $2^3 = 8$   | $2043 \times 8 = 16344$  |
| Extra Large | /16     | /20         | $2^{12} - 5 = 4091$ | $2^4 = 16$  | $4091 \times 16 = 65456$ |

> Each subnet reserves 5 IPs: network, broadcast, +3 AWS-reserved

---

## Sizing a VPC

1. **How many subnets are needed?**
2. **How many IPs per subnet are needed?**
3. **How many AZs will the VPC span?**

---

## VPC Design Process

1. **VPCs run services via subnets**, not directly.
2. **Each subnet lives in ONE Availability Zone.**

- When creating a subnet, you must choose a specific AZ (e.g., us-west-2a).
- A subnet’s IP range is limited to that AZ.
- You cannot span a subnet across multiple AZs.
- For HA and redundancy, replicate subnets across AZs.
- This is why we create one subnet per tier per AZ, e.g., Web-AZ-a, Web-AZ-b, etc.

### Default Strategy (Recommended)

| Design Element | Default                      | Reason                                  |
| -------------- | ---------------------------- | --------------------------------------- |
| AZs            | 3 + 1 spare = 4              | High availability and future expansion  |
| Tiers          | 3 + 1 spare = 4              | Common tiers: Web, App, DB + future use |
| Total Subnets  | 4 AZs × 4 tiers = 16 subnets | Duplication across zones                |

**VPC Sizing Result**:

- 16 subnets = `/20` each
- `/16` VPC = 65,536 IPs → enough for 16 × `/20` subnets

### VPC/Subnet CIDR Math

| VPC CIDR | Subnet CIDR | # of Subnets |
| -------- | ----------- | ------------ |
| /16      | /20         | 16           |
| /17      | /21         | 16           |
| /18      | /22         | 16           |

> Increase prefix by 1 → splits network in half

---

## A4L Global Network Plan

### AWS Regions Assumed:

- US Region 1, 2, 3
- Europe Region
- Australia Region

### AWS Accounts:

- General, Prod, Dev, +1 Reserved → 4 accounts

### VPC Allocation:

- Each region gets **16 × /16** networks
- Each account gets **4 × /16** networks per region → 4 VPCs per region/account

#### Region Block Assignments:

| Region          | /16 Range                   |
| --------------- | --------------------------- |
| US Region 1     | 10.16.0.0 – 10.31.255.255   |
| US Region 2     | 10.32.0.0 – 10.47.255.255   |
| US Region 3     | 10.48.0.0 – 10.63.255.255   |
| Europe          | 10.64.0.0 – 10.79.255.255   |
| Australia       | 10.80.0.0 – 10.95.255.255   |
| Reserved/Unused | 10.96.0.0 – 10.127.255.255  |
| GCP Reserved    | 10.128.0.0 – 10.255.255.255 |

---
