# 📘 SQL Journey – Chapter 1
## Introduction to Databases & SQL (FAANG Level)

> 🎯 Goal: Build a strong SQL foundation for FAANG interviews by understanding concepts, not just syntax.

---

# 📌 What is SQL?

**SQL (Structured Query Language)** is the standard language used to communicate with a **Relational Database Management System (RDBMS)**.

Using SQL, we can:

- Store Data
- Retrieve Data
- Update Data
- Delete Data
- Manage Database Objects
- Control User Permissions

---

# 📌 Why Do We Need SQL?

Imagine Amazon stores billions of records.

Without SQL:
- Finding one customer's order would be nearly impossible.

With SQL:
- Search data
- Filter records
- Sort results
- Join multiple tables
- Generate reports
- Perform calculations

---

# 📌 What is a Database?

A **Database** is an organized collection of related data.

### Example

Netflix stores

- Users
- Movies
- Watch History
- Subscription Plans

Amazon stores

- Customers
- Orders
- Products
- Payments

Instagram stores

- Users
- Posts
- Followers
- Comments
- Likes

---

# 📌 What is DBMS?

A **Database Management System (DBMS)** is software used to create, manage, and manipulate databases.

### Examples

- MySQL
- PostgreSQL
- Oracle
- SQL Server
- SQLite

---

# 📌 What is RDBMS?

**RDBMS (Relational Database Management System)** stores data in **tables** and creates relationships between them using **Keys**.

### Example

### Students Table

| ID | Name | Branch |
|----|------|---------|
|101|Rahul|CSE|
|102|Ankit|IT|

### Courses Table

| CourseID | Course |
|----------|---------|
|11|SQL|
|12|Python|

These tables can be connected using **Keys**.

---

# 📌 SQL is Declarative

SQL tells the database

> **WHAT** data you want,

not

> **HOW** to retrieve it.

Example

```sql
SELECT *
FROM Students;
```

The database engine automatically decides the best execution plan.

---

# 📌 How SQL Works Internally

```
User
 │
 ▼
SQL Query
 │
 ▼
Parser
 │
 ▼
Optimizer
 │
 ▼
Execution Engine
 │
 ▼
Storage Engine
 │
 ▼
Database
 │
 ▼
Result
```

---

## 🔹 Parser

Checks

- SQL Syntax
- Table Names
- Column Names

---

## 🔹 Optimizer

Chooses the fastest execution plan.

Examples

- Full Table Scan
- Index Scan
- Hash Join
- Nested Loop Join

---

## 🔹 Execution Engine

Executes the optimized query.

---

## 🔹 Storage Engine

Reads data from

- Memory
- Disk

Returns the final result.

---

# 📌 SQL Categories

## 1️⃣ DDL (Data Definition Language)

Used to create or modify database objects.

Commands

```sql
CREATE
ALTER
DROP
TRUNCATE
RENAME
```

---

## 2️⃣ DML (Data Manipulation Language)

Used to manipulate records.

Commands

```sql
INSERT
UPDATE
DELETE
```

---

## 3️⃣ DQL (Data Query Language)

Used to retrieve data.

Command

```sql
SELECT
```

---

## 4️⃣ TCL (Transaction Control Language)

Used to manage transactions.

Commands

```sql
COMMIT
ROLLBACK
SAVEPOINT
```

---

## 5️⃣ DCL (Data Control Language)

Used for access control.

Commands

```sql
GRANT
REVOKE
```

---

# 📌 What SQL Can Do?

✅ Create Databases

✅ Create Tables

✅ Insert Data

✅ Retrieve Data

✅ Update Records

✅ Delete Records

✅ Join Multiple Tables

✅ Perform Calculations

✅ Generate Reports

---

# 📌 Example SQL Queries

### Display all students

```sql
SELECT *
FROM Students;
```

---

### Display only Name column

```sql
SELECT Name
FROM Students;
```

---

### Students older than 20

```sql
SELECT *
FROM Students
WHERE Age > 20;
```

---

### Sort by Age (Descending)

```sql
SELECT *
FROM Students
ORDER BY Age DESC;
```

---

### First 5 Students

```sql
SELECT *
FROM Students
LIMIT 5;
```

---

# 📌 Database vs DBMS

| Database | DBMS |
|----------|------|
|Collection of data|Software that manages data|
|Stores information|Creates, updates, and controls databases|

---

# 📌 DBMS vs RDBMS

| DBMS | RDBMS |
|------|--------|
|Relationships are optional|Relationships are mandatory|
|Less strict|Supports constraints and normalization|
|No Foreign Keys|Supports Foreign Keys|
|Example: File System|MySQL, PostgreSQL, Oracle|

---

# 📌 Advantages of SQL

- Easy to Learn
- Fast Query Processing
- Industry Standard
- Highly Secure
- Supports Large Databases
- Data Integrity
- ACID Transactions
- Powerful Reporting

---

# 📌 Common FAANG Interview Questions

### Q1. What is SQL?

SQL is the standard language used to communicate with relational databases.

---

### Q2. Difference between Database and DBMS?

**Database:** Collection of organized data.

**DBMS:** Software used to manage databases.

---

### Q3. Difference between DBMS and RDBMS?

RDBMS supports relationships, keys, constraints, and normalization.

---

### Q4. Is SQL a Programming Language?

No.

SQL is a **Declarative Query Language**.

---

### Q5. Why is SQL Faster than Manual Searching?

Because databases use

- Query Optimizer
- Indexes
- Execution Plans
- Caching

to retrieve data efficiently.

---

# 🎯 Chapter Summary

✅ SQL is the language used to communicate with relational databases.

✅ Database stores data.

✅ DBMS manages the database.

✅ RDBMS stores related data in tables.

✅ SQL is declarative (WHAT, not HOW).

✅ SQL execution flow:

```
Parser
   ↓
Optimizer
   ↓
Execution Engine
   ↓
Storage Engine
   ↓
Result
```

✅ SQL Categories

- DDL
- DML
- DQL
- TCL
- DCL

---

# 📚 Next Chapter

## 🔑 Chapter 2 – Keys (FAANG Level)

Topics Covered

- Primary Key
- Candidate Key
- Super Key
- Alternate Key
- Composite Key
- Foreign Key
- Natural Key
- Surrogate Key
- Internal Working of Keys
- Index Relationship
- FAANG Interview Questions
- Hands-on SQL Problems

---

⭐ **If you're preparing for FAANG Data Engineering, Data Analyst, Backend, or Database interviews, mastering these fundamentals is essential before moving to Joins, Window Functions, Indexing, and Query Optimization.**
