# Gateway Load Balancers (GWLB)

**Gateway Load Balancers (GWLB)** are a relatively new AWS offering, designed to help you **deploy, scale, and manage third-party virtual network appliances** (e.g., firewalls, IDS/IPS, deep packet inspection) **transparently** and **elastically**.

---

## Why Use a Gateway Load Balancer?

### Problem:
- A public application (e.g., Catagram) needs **inline, transparent traffic inspection** for **inbound and outbound security**.
- Adding security appliances **manually** is:
  - **Tightly coupled**
  - **Hard to scale**
  - **Complex to manage**, especially in multi-tenant environments

### Solution:
- **Gateway Load Balancers** provide:
  - Scalable deployment of 3rd-party security appliances
  - Centralized inspection
  - Network-level transparency

---

## GWLB Use Cases

- Inline **firewalls**
- **Intrusion detection/prevention systems (IDS/IPS)**
- **Packet inspection**
- **Data loss prevention (DLP)**
- **Custom packet analysis**

---

## Components of a GWLB Deployment

### 1. **Gateway Load Balancer Endpoint (GWLBe)**
- Similar to **interface endpoints**, but:
  - Can be used in **route tables**
  - Acts as the **next hop** for routed traffic
- Deployed in **spoke VPCs** where traffic originates or terminates

### 2. **Gateway Load Balancer (GWLB)**
- Acts like a **Layer 3/4 load balancer**
- Distributes traffic to backend **EC2 appliances** (e.g., firewall VMs)
- Uses **Geneve protocol** to **encapsulate packets**
- Forwards **unaltered packets** through a tunnel to backend instances

---

## Packet Flow: How GWLB Works

### Inbound Request Flow

1. Client device initiates request to public-facing **Application Load Balancer (ALB)**.
2. Traffic enters via **Internet Gateway (IGW)**.
3. IGW uses **gateway route table** → routes to **GWLB Endpoint**.
4. GWLBe → forwards traffic to **GWLB**.
5. GWLB → encapsulates packets using **Geneve** → sends to security appliance.
6. Appliance inspects packets → returns them to GWLB.
7. GWLB → removes encapsulation → sends packets back via GWLBe.
8. GWLBe → routes to **ALB** → forwards to **backend EC2 instance**.

### Outbound Response Flow

1. Response from EC2 instance → returns via **ALB**.
2. Subnet route: default route → **GWLBe**.
3. GWLBe → forwards to **GWLB** → encapsulated and sent to appliance.
4. Appliance inspects → returns to GWLB.
5. GWLB → removes encapsulation → returns to GWLBe.
6. GWLBe subnet: default route → **Internet Gateway**.
7. IGW NATs source IP to ALB public IP → sends response to client.

---

## Why Use the Geneve Protocol?

- **Encapsulation protocol** that preserves:
  - Source/destination IPs
  - Source/destination ports
  - Payload contents

- Benefits:
  - Allows full packet analysis
  - Ensures transparent inspection
  - Maintains traffic flow consistency

---

## Benefits of GWLB

| Feature                     | Description                                                             |
|-----------------------------|-------------------------------------------------------------------------|
| Transparent Security        | GWLB sits inline without modifying original traffic                     |
| Horizontal Scaling          | GWLB load balances across multiple security appliances                  |
| Fault Tolerance             | Failed appliance? GWLB reroutes traffic to a healthy one                |
| Flow Stickiness             | Same session always uses the same appliance (stateful flow awareness)   |
| Vendor Flexibility          | Run any compatible 3rd-party appliance (e.g., Palo Alto, Fortinet)      |
| Centralized Architecture    | Easily used in **hub-spoke**, **multi-VPC**, or **multi-account** setups|

---

## Common Architecture Pattern

### App VPC:
- Public subnets: ALB
- Private subnets: App servers
- **GWLBe** in route table
- App traffic is routed through GWLB for inspection

### Security VPC:
- **GWLB**
- **Security appliance fleet** (EC2 in ASG)
- Handles inspection, filters traffic
- Fully decoupled and scalable

---