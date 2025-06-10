# Amazon Elastic File System (EFS)

## 🧾 Overview

**Amazon EFS (Elastic File System)** is a fully managed NFS (v4) file system that can be mounted on **Linux EC2 instances** and shared across **multiple instances simultaneously**.

In our example use case (Animals for Life WordPress site), EFS stores media **outside** EC2 instances, allowing stateless compute and enabling scaling and self-healing architecture.

---

## 🏗️ Architecture

### 📂 File System Basics

- EFS is based on the **NFS v4 protocol**.
- You create **EFS file systems** which are mounted in Linux instances (e.g., `/nfs/media`).
- Multiple EC2 instances can mount the same file system concurrently.

### 🧱 Components

| Component       | Description |
|----------------|-------------|
| **File System** | Core storage unit, POSIX-compliant |
| **Mount Targets** | Access points for EFS, created per **subnet** in a **VPC** |
| **POSIX Permissions** | Standard Linux file permission model |

> 🔁 EFS is **decoupled** from EC2, similar to EBS, but provides **file-level** rather than block-level access.

---

## 🌐 Access & Connectivity

- EFS is **VPC-scoped** (private by default).
- Can be accessed from **on-premises** via:
  - **VPC Peering**
  - **VPN**
  - **Direct Connect**
- **Highly available** if you create **one mount target per AZ**.

---

## 📊 Performance Modes

| Mode         | Description |
|--------------|-------------|
| **General Purpose** (default) | Low latency, suitable for web servers, CMS, general workloads |
| **Max IO** | High throughput & IOPS, higher latency; good for big data, media, analytics |

---

## 🚀 Throughput Modes

| Mode         | Description |
|--------------|-------------|
| **Bursting** (default) | Throughput scales with file system size (like **GP2** volumes) |
| **Provisioned** | Manually set throughput, independent of file system size (like **IO1** volumes) |

---

## 🧊 Storage Classes

| Class              | Description |
|--------------------|-------------|
| **Standard** (default) | For frequently accessed files |
| **Infrequent Access (IA)** | For data accessed less frequently, lower cost |

- You can use **Lifecycle Policies** to automatically move files between storage classes (like in S3).

---

## 🧠 Exam Tips

- EFS is for **Linux only**
- Mount targets require **1 per AZ** for **HA**
- Performance/throughput trade-offs are like **EBS (GP2/IO1)** or **S3 (Standard/IA)**
- Useful when you want:
  - **Shared access** to file system data
  - **Stateless EC2** architecture
  - **Scalable media storage** for web apps

---

## ✅ Summary

| Feature | Description |
|--------|-------------|
| Protocol | NFS v4 |
| OS Support | Linux only |
| Access | Via mount targets in VPC |
| Scalability | Supports thousands of concurrent connections |
| Hybrid Support | VPN, Direct Connect, VPC Peering |
| Use Cases | Web servers, CMS, home dirs, big data (with Max IO), shared storage |

> 🎯 EFS enables shared file systems for EC2-based applications, ideal for scaling and ensuring data durability across ephemeral compute resources.

