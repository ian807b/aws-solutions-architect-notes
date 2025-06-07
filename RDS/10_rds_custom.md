# RDS Custom

## 1. What Is RDS Custom?

- **Hybrid solution** between:

  - **Fully managed RDS**
  - **Self-managed DB on EC2**

- Offers more **customization and control** than RDS, but with less overhead than EC2.
- Intended for use cases where deeper access to OS or DB engine is required.

---

## 2. Supported Engines

- ✅ **Microsoft SQL Server**
- ✅ **Oracle**
- ❌ Not supported for MySQL, PostgreSQL, MariaDB, etc.

---

## 3. Access Capabilities

- Allows direct access via:

  - **SSH**
  - **RDP**
  - **AWS Systems Manager Session Manager**

- Provides access to:
  - **Operating system**
  - **Database engine**

---

## 4. Visibility & Ownership

| Feature        | Standard RDS   | RDS Custom                 |
| -------------- | -------------- | -------------------------- |
| EC2 Instance   | ❌ Not visible | ✅ Visible in your account |
| EBS Volumes    | ❌             | ✅                         |
| S3 Backups     | ❌             | ✅                         |
| Network Access | ENIs injected  | Fully managed in your VPC  |

> 🔎 RDS Custom resources run inside **your AWS account**, unlike standard RDS which is hosted in an AWS-managed environment.

---

## 5. Database Automation Settings

- Must **pause automation** when performing customizations
- Resume automation after changes to **re-enable RDS-managed features**
- Avoid conflicts with AWS automation during manual changes

---

## 6. Service Model Comparison

The level of customer vs AWS responsibility changes depending on the deployment model:

| Component           | On-Premises | EC2      | RDS Custom | RDS      |
| ------------------- | ----------- | -------- | ---------- | -------- |
| Hardware            | Customer    | AWS      | AWS        | AWS      |
| OS Install/Patching | Customer    | Customer | Shared     | AWS      |
| DB Patching         | Customer    | Customer | Shared     | AWS      |
| Backups             | Customer    | Customer | Shared     | AWS      |
| HA/Scaling          | Customer    | Customer | Shared     | AWS      |
| App Optimization    | Customer    | Customer | Customer   | Customer |

- **RDS Custom** provides:
  - **More control** than RDS
  - **Less management burden** than EC2
  - **Shared responsibility** model

---

## 7. Summary

| Feature            | Description                                           |
| ------------------ | ----------------------------------------------------- |
| Purpose            | Bridge gap between RDS and EC2-hosted DBs             |
| Access             | Full (OS + DB engine) via SSH, RDP, Session Manager   |
| Account Visibility | EC2, EBS, S3 visible in **your** account              |
| Automation         | Must be paused during customizations                  |
| Use Cases          | Niche scenarios where standard RDS is too restrictive |
| Supported Engines  | Oracle, MS SQL Server only                            |

> 🧠 **Exam Tip**:  
> Just knowing **RDS Custom exists**, what it does, and how it differs from standard RDS is sufficient.

---
