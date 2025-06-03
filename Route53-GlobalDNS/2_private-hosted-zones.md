# 🧾 Route 53 – Private Hosted Zones

## 📌 Summary

A **Private Hosted Zone (PHZ)** in Route 53 is a DNS zone that is **only accessible within associated VPCs**. It functions similarly to a public hosted zone but is **not resolvable over the public internet**.

---

## 🔧 Key Concepts

### ✅ What is a Private Hosted Zone?

- A **DNS database** hosted in Route 53 that is only accessible from **within AWS VPCs**.
- It allows you to **create DNS records** (e.g., A, CNAME, MX) just like a public zone.
- DNS queries are resolved using the **Route 53 resolver** (typically `VPC+2` address).
- Not accessible from the **public internet**.

---

### ✅ VPC Association

- A PHZ must be **explicitly associated** with one or more VPCs.
- VPCs can be in:
  - The **same AWS account** (via console, CLI, or API)
  - **Different accounts** (CLI and API only)
- Only associated VPCs can resolve DNS records from the PHZ.

---

### 🔐 Use Cases

- Internal-only applications (e.g., intranets, internal APIs, private services)
- Multi-tier apps with internal components (DB, app servers)
- Environment isolation (e.g., test, staging, prod using the same domain)

---

## 🔄 Split-Horizon DNS (Split-View)

> Use the **same domain name** for both public and internal access, but expose **different DNS records**.

### 📘 Example: `animals4life.org`

| Environment      | Hosted Zone Type      | Accessible Records                                   |
|------------------|------------------------|-----------------------------------------------------|
| Public users     | **Public Hosted Zone** | `www.animals4life.org`, `mail.animals4life.org`     |
| Internal users   | **Private Hosted Zone**| `accounting.animals4life.org`, `db.animals4life.org`|

- Public queries follow the usual path: ISP → Root → `.org` → Route 53 public NS.
- Internal queries go directly to Route 53 via VPC resolver and access the **private zone**.
