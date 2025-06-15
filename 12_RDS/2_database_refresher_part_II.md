# Database Refresher Part II

## Relational vs Non-Relational Databases

Databases are systems that store and manage data, but they differ significantly in terms of how they store, manage, and retrieve that data. They are broadly categorized into:

- **Relational (SQL)**
- **Non-Relational (NoSQL)**

### Relational Databases (SQL / RDBMS)

- Use **tables** with rows and columns.
- Structured via **schemas**, which define the structure (attribute names, types, valid values).
- Relationships between tables are **fixed and defined in advance**.
- Use **primary keys** (unique per row) and **foreign keys** to model relationships.
- Ideal for structured data with consistent relationships.

**SQL** databases are not ideal for rapidly changing relationships (e.g., social media).

---

## NoSQL Database Types

### 1. Key-Value Databases

- Consist of key-value pairs with no defined structure.
- No relationships, no schema.
- Extremely scalable and fast.
- Use cases: **caching**, **simple lookups**, **in-memory stores**.
- AWS example: **Amazon ElastiCache (Redis)**

#### Example:

```json
{
  "2025-06-01T10:00:00": 15,
  "2025-06-01T11:00:00": 20
}
```

### 2. Wide Column Stores

- Variation of key-value stores.
- Use **partition keys** (and optionally sort keys).
- Flexible schema: each item can have different attributes.
- Fast and horizontally scalable.
- AWS example: **Amazon DynamoDB**

#### Key Characteristics:

- **Same key layout required**, attributes can vary.
- Ideal for: **IoT, web-scale apps, gaming, ad tech**.

### 3. Document Databases

- Store data as **documents** (JSON/XML).
- Each document is accessed via a unique ID.
- Database understands document structure for indexing/querying.
- Great for: **catalogs, user profiles, CMS, orders**.
- AWS example: **Amazon DocumentDB (with MongoDB compatibility)**

### 4. Column-Oriented Databases

- Store data **by column**, not by row.
- Optimized for **analytics and reporting** (OLAP).
- Not suitable for transactional workloads.
- AWS example: **Amazon Redshift**

#### Example Use Case:

- Aggregate queries on single columns: sizes sold, product sales.
- Efficient for: **data warehousing, BI tools**.

### 5. Graph Databases

- Nodes = entities (people, cities, companies)
- Edges = relationships (works_for, lives_in), with **direction and properties**
- Data **and** relationships are stored in the database.
- Ideal for: **social networks, HR systems, fraud detection**
- AWS example: **Amazon Neptune**

#### Benefits:

- Faster relationship queries than SQL joins.
- Relationship data is **precomputed**.

---

## Summary Table

| Type              | Structure             | Ideal For                             | AWS Example          |
| ----------------- | --------------------- | ------------------------------------- | -------------------- |
| Relational (SQL)  | Tables, schema        | Structured data, transactions         | RDS (MySQL/Postgres) |
| Key-Value         | Key + Value           | Caching, session data                 | ElastiCache          |
| Wide Column Store | Keys + flexible attrs | IoT, web-scale workloads              | DynamoDB             |
| Document Store    | JSON/XML Docs         | Catalogs, profiles, orders            | DocumentDB           |
| Column-Oriented   | Columns               | Analytics, BI                         | Redshift             |
| Graph             | Nodes + Edges         | Social networks, relationship queries | Neptune              |

---

For exam prep:

- Choose **Key-Value** for simplicity and performance.
- Choose **Wide Column** for flexible, semi-structured workloads.
- Choose **Document** for flexible data with nested fields.
- Choose **Column** for analytics and reporting.
- Choose **Graph** for highly connected data.
- Choose **Relational** for structured, transactional applications.
