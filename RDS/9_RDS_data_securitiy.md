# RDS Data Security

## 1. Encryption in Transit

- All RDS engines support **encryption in transit** using **SSL/TLS**.
- You can configure this to be **mandatory on a per-user basis**.
- Protects data traveling between the client and the RDS instance.

---

## 2. Encryption at Rest

Encryption at rest in RDS can be implemented in **two main ways**:

### A. KMS + EBS-Based Encryption (Default Method)

- **Applies to** all supported RDS engines
- Uses:
  - **AWS KMS** to manage **Customer Master Keys (CMKs)**
  - **EBS** for encrypted storage
- Process:
  - CMKs generate **Data Encryption Keys (DEKs)**
  - DEKs are loaded to the **RDS host**
  - The host encrypts/decrypts data (RDS engine is unaware)
- Encrypts:
  - **Storage**
  - **Logs**
  - **Snapshots**
  - **Replicas**
- ❗ Once enabled, **encryption cannot be removed**

### B. Transparent Data Encryption (TDE)

- Supported by **Microsoft SQL Server** and **Oracle**
- Encryption occurs **inside the database engine**
- Enhanced trust model:
  - No reliance on host-level encryption
  - Ensures encryption from engine to disk

### C. TDE + AWS CloudHSM (for Oracle)

- Offers **stronger key control**:
  - Keys stored in **CloudHSM** (not accessible to AWS)
  - Full **customer ownership of key lifecycle**
- Designed for high-compliance and **regulatory environments**

---

## 3. RDS IAM Authentication (Optional)

> ⚠️ **IAM authentication is for authentication only, not authorization**

### How It Works

1. **RDS Instance**:

   - Enable IAM authentication
   - Create a **local DB user** that will use IAM token

2. **IAM Identity (User or Role)**:

   - Attach IAM policy that maps to the DB user
   - Policy allows `rds-db:connect` permission

3. **Login Process**:
   - IAM identity generates an **authentication token** via `generate-db-auth-token`
   - Token is **valid for 15 minutes**
   - Token used as password when connecting to the RDS DB

> 🧠 RDS database **authorization** (e.g., read/write permissions) is still managed via the **local database engine**, not IAM.

---

## 5. Summary

| Category                  | Description                                                              |
| ------------------------- | ------------------------------------------------------------------------ |
| **Authentication**        | Default via DB credentials; optionally via IAM token                     |
| **Authorization**         | Always handled by DB-level user permissions                              |
| **Encryption in Transit** | SSL/TLS between client and DB                                            |
| **Encryption at Rest**    | EBS + KMS (default), TDE (SQL/Oracle), or TDE + CloudHSM (Oracle)        |
| **Key Management**        | AWS-managed or customer-managed CMKs via KMS; full control with CloudHSM |

---
