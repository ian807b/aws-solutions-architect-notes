# VPC Routing, Internet Gateway, and Bastion Hosts

**Internet Gateway** enables access to/from the public internet and AWS public zone.

---

## VPC Router: The Foundation of Routing

- A **VPC router** is present in all VPCs (default or custom).
- It is:
  - **Highly available**
  - **Automatically deployed** in all used AZs
  - **Unmanaged** (no availability concerns)

### Function
- **Moves traffic between subnets** within the same VPC
- Every subnet has the **network + 1 IP** reserved for the **VPC router interface**

---

## Route Tables

- VPCs come with a **Main Route Table**
- You can create **custom route tables** and associate them with subnets

### Rules:
| Component                     | Behavior                                                                 |
|------------------------------|--------------------------------------------------------------------------|
| Subnet → Route Table         | A subnet can be associated with **only one** route table at a time      |
| Route Table → Subnets        | A route table can be associated with **many subnets**                   |
| No explicit association      | Subnet uses the **Main Route Table**                                     |
| Custom association           | **Overrides** Main Route Table                                           |

### Route Matching
- Route Table contains a **list of routes**
- When **traffic leaves a subnet**, the VPC router:
  1. Looks at the **destination IP**
  2. Matches it against entries in the **route table**
  3. Selects the **most specific match** (higher prefix = more specific)
    - `/32` = most specific
    - `0.0.0.0/0` = default catch-all route

### Route Selection Priority
- If multiple routes match:
  - The one with the **longest prefix match** is selected
- Exception: **Local routes always take priority**

### Targets in Route Table
- **`local`** = within VPC
- **Gateways** = e.g., Internet Gateway (IGW)

### Local Routes
- Always present and **uneditable**
- Match the **VPC's IPv4 and/or IPv6 CIDR**
- Always take priority

---

## Internet Gateway (IGW)

### What is it?
- A **regionally resilient gateway**
- Sits at the **border** between:
  - The VPC
  - The **public internet** and **AWS public zone**

### Key Facts:
| Fact                            | Detail                                                                    |
|---------------------------------|---------------------------------------------------------------------------|
| AZ Dependency                   | None. One IGW covers **all AZs** in the VPC's region                      |
| VPC Attachment                  | **One-to-one**: A VPC can have 0 or 1 IGW                                 |
| IGW Unattached State            | Can exist without a VPC attachment                                        |
| Target for Route Table          | Used in **default routes** (`0.0.0.0/0` or `::/0`) IGW is the next hop for all internet-bound traffic                         |
| Managed                         | AWS handles performance and availability                                  |

### Purpose
- Enables **communication between public internet and EC2 instances**
- Enables **access to AWS public services** like S3, SNS, etc., via **AWS public zone**

---

## Making a Subnet Public (IPv4 & IPv6)

To make a subnet public:
1. **Create & attach** an Internet Gateway to the VPC.
2. **Create a custom route table**.
3. **Add default routes** for:
   - IPv4 → `0.0.0.0/0`
   - IPv6 → `::/0` (if using IPv6)
4. **Set Internet Gateway as target**.
5. **Associate** the route table with the **web tier subnets**.
6. **Configure subnet settings** to auto-assign:
   - **Public IPv4**
   - (Optional) **IPv6** addresses

> Once complete, EC2 instances in these subnets with public IPs can:
> - Access the internet
> - Be accessed from the internet

---

## Public IPv4 Addressing Internals

### Key Insight:
> EC2 instances **never see** their public IPv4 addresses.

- When an EC2 instance is given a **public IPv4**, the mapping exists **only in the IGW**.
- OS on the instance is configured with **private IP only**.
- Traffic is **NAT-ed (Network Address Translation)** by the IGW.

### Data Flow (IPv4):
1. EC2 instance sends a packet with **private source IP**.
2. IGW:
   - **Translates source IP** to public IP
   - Forwards to destination
3. Reply packet:
   - Destined to public IP
   - IGW **translates destination** to private IP
   - Sends to EC2 instance

### Exam Tip:
> **Do not assume** the public IP is configured in the EC2 OS for IPv4.  
> The OS doesn't know the public IP.

---

## IPv6 Differences

- **Publicly routable by default**
- No NAT or translation required
- OS is configured with the **public IPv6 address**
- IGW simply forwards packets **as-is**

---

## Bastion Hosts (Jump Boxes) - Legacy Architecture

- An EC2 instance in a **public subnet**
- Acts as a **secure entry point** to access private resources in a VPC

### Use Cases:
- **Inbound management access**
- Connect to private EC2 instances (e.g., via SSH)

### Security Considerations:
- Restrict to **trusted IPs**
- Use **SSH key pairs** or integrate with **identity servers**

> Historically the **only way** to manage private EC2s.  
> Still appears frequently on the **exam** and in **legacy architectures**.

---

