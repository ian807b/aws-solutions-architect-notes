# Elastic Load Balancer (ELB) Architecture

## Overview

This lesson serves as a foundation for understanding the **architecture of Elastic Load Balancers (ELBs)**. We’ll explore the high-level, logical, and physical architecture of ELBs—both as a refresher and as an introduction to finer points of load balancing.

---

## What Does a Load Balancer Do?

- Accepts incoming connections from clients.
- Distributes those connections across registered backend compute resources.
- Abstracts users from the underlying infrastructure.
- Supports dynamic scaling (up/down) without user impact.
- Handles failure and recovery transparently from the user's perspective.

---

## Basic Architecture

- **Environment**: A VPC with two Availability Zones (AZA and AZB).
- **Subnets**: Two public subnets (one in each AZ) and some private subnets.
- **User**: Bob (example user) initiates a connection.

---

## Load Balancers in Action

- ELB receives connections and forwards them to backend services (typically EC2).
- **Application Load Balancers (ALB)** support multiple types of compute—not just EC2.

---

## Configuration Options

When provisioning an ELB, you must configure:

1. **IP Version**:
   - IPv4 only
   - Dual-stack (IPv4 + IPv6)

2. **Availability Zones**:
   - You choose one subnet per Availability Zone.
   - These subnets define where **load balancer nodes** are deployed.

---

## Load Balancer Nodes

- Each ELB is backed by **multiple nodes** across selected subnets.
- When created, a **DNS name (A record)** is generated.
  - This A record resolves to all the nodes.
  - Incoming requests are **evenly distributed** across all nodes.
- Nodes are:
  - **Highly available** (spread across AZs).
  - **Scalable** (more nodes are added as load increases).
  - **Self-healing** (failed nodes are automatically replaced).

---

## Internet-Facing vs. Internal Load Balancers

| Type              | Public IPs | Private IPs | Use Case |
|-------------------|------------|-------------|----------|
| Internet-Facing    | ✅          | ✅           | External traffic (e.g., web apps) |
| Internal           | ❌          | ✅           | Internal communication (e.g., app-to-app) |

- **Functionally identical**, except for IP addressing.
- Internet-facing ELBs are needed to serve public traffic.
- Internal ELBs are used to isolate and scale application tiers.

---

## Listener Configuration

- Controls **protocols and ports** the ELB listens to.
- Defined per **listener**, which is a front-end configuration.
- (More on this in a dedicated lesson later.)

---

## Backend Instance Connectivity

- ELBs can route to:
  - Public EC2 instances
  - Private EC2 instances
- **Internet-facing ELBs** do **not** require public EC2 instances as targets.
  - They only need **network connectivity** to those instances.
  - This is commonly misunderstood in the exam.

> ❗️Important: In the exam, remember that the only requirement is that **ELB nodes can reach backend instances**, regardless of their public/private IP status.

---

## Subnet Size Requirements

- ELBs need **8+ free IP addresses per subnet**.
- IP math:
  - `/28` = 16 IPs - 5 reserved = 11 usable
  - `/27` = 32 IPs - 5 reserved = 27 usable

> ✅ **AWS recommends using at least a /27 subnet** for scalability.

However:
- `/28` is technically acceptable as a **minimum size**.
- **For the exam**, remember:
  - **/27 is preferred**
  - **/28 is still a valid answer** if it's the only option listed

---

## Multi-Tier Architecture Example

- Bob → Internet-Facing ELB → Web Servers
- Web Servers → **Internal ELB** → App Servers

This structure allows:
- Separation of tiers
- Independent scaling
- Better security and architecture practices

---

## End of Part 1

This is the conclusion of part one of the ELB architecture lesson. Take a short break before continuing to part two.
