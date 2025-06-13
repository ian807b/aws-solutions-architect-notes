# Advanced Demo: Architecture Evolution – WordPress on AWS

## Objective

To **evolve a basic WordPress deployment** from a single EC2 instance into a **fully elastic, scalable, and resilient architecture** by progressively integrating AWS services.

---

## Step-by-Step Architecture Evolution

### 🔹 Step 1: Manual Deployment

- Launch a **single EC2 instance**
- Manually install and configure:
  - WordPress (application)
  - MySQL (database)
  - Local storage (for media and blog content)

---

### 🔹 Step 2: Automate with Launch Template

- Use a **Launch Template** to replicate the manual EC2 configuration.
- Still using **a single instance**, but with automated provisioning.

---

### 🔹 Step 3: Migrate Database to Amazon RDS

- Move MySQL database from EC2 to a **managed RDS instance**.
- Application logic stays on EC2, but **data is now persistent and decoupled**.

---

### 🔹 Step 4: Migrate Storage to Amazon EFS

- Provision **Elastic File System (EFS)**
- Migrate WordPress content (uploads, media, etc.) from local EC2 disk to **EFS**

> Now, multiple EC2 instances can **share the same file content**

---

### 🔹 Step 5: Enable Scalability with ASG + ALB

- Create an **Auto Scaling Group (ASG)** with multiple WordPress instances
- Configure an **Application Load Balancer (ALB)** in front of the ASG
- Users access the blog via the ALB, not directly via EC2

**Key Benefits:**
- Full **elasticity**: scale out/in based on traffic
- **Self-healing**: failed instances automatically replaced
- **High availability**: multi-AZ setup with redundant paths

---

## End Result: Production-Grade Architecture

| Component     | AWS Service            |
|---------------|------------------------|
| Web Servers   | EC2 (in ASG)           |
| Load Balancing| Application Load Balancer (ALB) |
| Database      | Amazon RDS (MySQL)     |
| Shared Storage| Amazon EFS             |
| Scaling       | Auto Scaling Group     |

---