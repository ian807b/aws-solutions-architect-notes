# Database Refresher (Part 1)

## What is a Database?

- A **database** is a system that stores and manages data.
- There are many types of databases, with key differences in:

  - How data is stored on disk
  - How data is retrieved and managed in memory
  - How data is queried and presented to users

---

## Two Broad Types of Databases

### 1. Relational (SQL)

- Often referred to as **SQL** databases, although technically SQL is the language used (Structured Query Language).
- Also referred to as **RDBMS** (Relational Database Management System).

#### Key Characteristics

- **Schema-based**: Schema is fixed and defined _before_ inserting any data.
- **Tables**: Core data structure; each table has:

  - **Columns** (attributes)
  - **Rows** (records)

- **Primary Key**: Each table must have a unique identifier per row.
- **Relationships** between tables are defined using **foreign keys**.
- Enforces **data integrity and consistency**.

#### Example: Pet Database

- **Human Table**

  - Attributes: `ID`, `Fname`, `Lname`, `Age`
  - Each row has a unique `ID` (Primary Key)

- **Animal Table**

  - Attributes: `AID`, `Name`, `Type`
  - Each row has a unique `AID` (Primary Key)

- **Join Table** (many-to-many relationships): `HumanAnimal`

  - Composite Key: (`HumanID`, `AnimalID`)
  - Links humans to their pets

#### Limitations

- Schema must be declared and enforced upfront.
- Changing relationships or schema post-deployment is difficult.
- Less flexible for rapidly changing data models (e.g., social networks).

---

### 2. Non-Relational (NoSQL)

- **NoSQL** encompasses all database types that do not follow the relational model.
- It's not one single technology—includes multiple data models:

  - Document stores
  - Key-value stores
  - Column-family stores
  - Graph databases

#### Common Traits

- **Weak or no schema**: Data structure is flexible.
- **Dynamic relationships**: Relationships are not fixed in advance.
- Designed for:

  - High scalability
  - High availability
  - Rapid iteration of data models

---

## Summary

| Feature        | SQL (Relational)                   | NoSQL (Non-Relational)                  |
| -------------- | ---------------------------------- | --------------------------------------- |
| Schema         | Fixed and declared in advance      | Weak or none                            |
| Data Structure | Tables with rows and columns       | Varies: documents, key-value, etc.      |
| Relationships  | Predefined, foreign/composite keys | Flexible, often implicit                |
| Use Cases      | Structured, consistent data        | Dynamic, rapidly evolving relationships |
| Examples       | MySQL, PostgreSQL, Oracle          | MongoDB, DynamoDB, Cassandra, Neo4j     |
