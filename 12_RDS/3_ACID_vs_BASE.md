# ACID vs BASE Transaction Models in Databases

There're two database transaction models—**ACID** and **BASE**—that are fundamental to understanding how different database systems handle transactions. These models relate closely to the **CAP Theorem**, which asserts that a distributed system can provide only two of the following three guarantees:

- **Consistency (C):** Every read receives the most recent write or an error.
- **Availability (A):** Every request receives a (non-error) response—without guarantee that it contains the most recent write.
- **Partition Tolerance (P):** The system continues to operate despite arbitrary partitioning due to network failures.

Because you can only choose two out of these three in distributed systems, ACID and BASE offer different trade-offs.

---

## ACID Transactions

ACID stands for:

- **Atomicity**: All operations in a transaction succeed or none do. E.g., in a bank transfer, both debit and credit operations must complete or none at all.
- **Consistency**: Transactions take the database from one valid state to another, adhering to all rules (e.g., foreign key constraints).
- **Isolation**: Transactions don't interfere with each other. The result should be as if transactions were run sequentially.
- **Durability**: Once committed, the result of a transaction will survive permanently—even in case of power loss or crashes.

**Used By**: Relational databases like **RDS (MySQL, PostgreSQL, Oracle, SQL Server)**.

**Strengths:**

- Strong data integrity.
- Suitable for financial and mission-critical applications.

**Weaknesses:**

- Harder to scale horizontally.
- Performance can suffer in highly concurrent distributed environments.

---

## BASE Transactions

BASE stands for:

- **Basically Available**: System guarantees availability, potentially sacrificing consistency.
- **Soft state**: The system state may change over time, even without input, due to eventual propagation.
- **Eventually Consistent**: The system will become consistent over time, given no new updates.

**Used By**: NoSQL databases like **DynamoDB**, **Cassandra**, **MongoDB**.

**Strengths:**

- Highly scalable.
- Supports high availability across distributed systems.

**Weaknesses:**

- Potentially inconsistent reads.
- Developer must handle consistency in application logic.

---

## Summary Table

| Property         | ACID                                 | BASE                                 |
| ---------------- | ------------------------------------ | ------------------------------------ |
| Focus            | Consistency                          | Availability                         |
| Use Case         | OLTP (Banking, Inventory)            | Big Data, Web Scale, Social Media    |
| Write Guarantees | Strong consistency                   | Eventual consistency                 |
| Scalability      | Limited (Vertical scaling preferred) | High (Horizontal scaling possible)   |
| Example DBs      | RDS (MySQL, PostgreSQL, etc.)        | DynamoDB, Cassandra, MongoDB         |
| Application Role | Minimal responsibility               | Needs to manage consistency manually |

---

## Summary

- If you see **ACID**, assume **RDS** unless otherwise specified.
- If you see **BASE**, think **NoSQL** (especially **DynamoDB**).
- **DynamoDB** supports both BASE (by default) and ACID (via [DynamoDB Transactions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html)).
- For highly available and scalable apps that can tolerate eventual consistency, BASE-based NoSQL is ideal.
- For strict data integrity (e.g., financial systems), prefer ACID-compliant RDBMS.

---
