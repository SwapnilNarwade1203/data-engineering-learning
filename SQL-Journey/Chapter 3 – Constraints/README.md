# 📘 SQL Journey – Chapter 3
# SQL Constraints (FAANG Level)

<p align="center">

![SQL](https://img.shields.io/badge/SQL-Constraints-blue?style=for-the-badge)

![Level](https://img.shields.io/badge/Level-FAANG-orange?style=for-the-badge)

![Database](https://img.shields.io/badge/Database-MySQL%20%7C%20PostgreSQL-green?style=for-the-badge)

</p>

---

# 📖 Table of Contents

- Introduction
- Why Constraints Exist
- What is a Constraint?
- Types of Constraints
- NOT NULL Constraint
- UNIQUE Constraint
- PRIMARY KEY Constraint
- FOREIGN KEY Constraint
- CHECK Constraint
- DEFAULT Constraint
- AUTO_INCREMENT
- Internal Working
- Constraint Violations
- SQL Examples
- Best Practices
- FAANG Interview Questions
- Practice Problems
- Summary

---

# 🎯 Learning Objectives

After completing this chapter you will be able to:

✅ Understand why databases require constraints

✅ Explain every SQL constraint confidently

✅ Design production-ready tables

✅ Prevent invalid data

✅ Understand how constraints work internally

✅ Answer FAANG interview questions

---

# 📌 Introduction

Imagine you are building Amazon.

Customers are placing millions of orders every day.

Now imagine the database allows:

- Customer ID = NULL
- Age = -15
- Salary = -5000
- Duplicate Email IDs
- Orders belonging to non-existing customers

Would Amazon work?

**No.**

The database would become inconsistent within minutes.

This is exactly why **Constraints** exist.

Constraints protect the database from invalid or inconsistent data.

They act as **rules** that every row must satisfy before being stored.

---

# 🤔 What is a Constraint?

A **Constraint** is a rule applied to one or more columns in a table.

The database automatically checks these rules whenever data is inserted, updated, or deleted.

If the rule is violated, the DBMS rejects the operation.

Think of constraints as **security guards** standing at the entrance of your database.

```
          INSERT
             │
             ▼
     Constraint Check
             │
      ┌──────┴──────┐
      │             │
   Valid         Invalid
      │             │
      ▼             ▼
 Stored       Error Returned
```

Unlike validation written in application code, constraints are enforced directly by the database, ensuring that **every application** accessing the database follows the same rules.

---

# 💡 Why Do We Need Constraints?

Without constraints, databases become unreliable.

Example:

Students Table

| Student_ID | Name | Age |
|------------|------|-----|
|101|Rahul|20|
|101|Rahul|20|
|NULL|Ankit|21|
|104|Priya|-5|

Problems:

❌ Duplicate Student IDs

❌ NULL Student ID

❌ Invalid Age

❌ Data inconsistency

Now imagine thousands of applications reading this data.

Every report, dashboard, and business decision becomes incorrect.

Constraints eliminate these problems automatically.

---

# 🎯 Advantages of Constraints

- Maintain data integrity
- Prevent duplicate records
- Prevent NULL values where not allowed
- Enforce business rules
- Maintain relationships between tables
- Improve data quality
- Reduce bugs in applications
- Protect the database from invalid inserts

---

# 🏢 Real World Examples

### Banking System

Balance cannot be negative.

Constraint:

```sql
CHECK (Balance >= 0)
```

---

### College Database

Student ID must be unique.

Constraint:

```sql
PRIMARY KEY(Student_ID)
```

---

### Amazon

Order must belong to an existing customer.

Constraint:

```sql
FOREIGN KEY(Customer_ID)
REFERENCES Customers(Customer_ID)
```

---

### Gmail

Every email address must be unique.

Constraint:

```sql
UNIQUE(Email)
```

---

### Employee Database

Employee Name cannot be NULL.

Constraint:

```sql
NOT NULL
```

---

# 📚 Types of SQL Constraints

SQL provides several built-in constraints.

| Constraint | Purpose |
|------------|----------|
| NOT NULL | Prevent NULL values |
| UNIQUE | Prevent duplicate values |
| PRIMARY KEY | Unique + NOT NULL |
| FOREIGN KEY | Create relationships |
| CHECK | Validate conditions |
| DEFAULT | Assign default values |
| AUTO_INCREMENT | Generate automatic IDs |

These constraints can be used individually or together while designing a table.

---

# 🧠 Internal Working

When you execute an INSERT statement, the database does **not** immediately save the row.

Instead, it performs a series of validation checks.

```
User Query
     │
     ▼
SQL Parser
     │
     ▼
Constraint Validation
     │
     ▼
Storage Engine
     │
     ▼
Data Stored
```

If any constraint fails, the operation is rolled back and an error is returned.

This validation happens before the data is permanently written to disk.

--- # 🔒 NOT NULL Constraint

## 📌 What is NOT NULL?

The **NOT NULL** constraint ensures that a column **cannot contain NULL values**.

It forces users to provide a value whenever a new record is inserted.

A NULL value means **"unknown"** or **"no value"**, not zero (`0`) or an empty string (`''`).

---

## 🤔 Why Do We Need NOT NULL?

Imagine an employee table.

| Employee_ID | Name | Salary |
|-------------|------|---------|
|101|Rahul|50000|
|102|NULL|60000|
|103|Ankit|NULL|

Problems:

- We don't know employee 102's name.
- We cannot calculate employee 103's salary.
- Reports become unreliable.
- Business rules break.

Using **NOT NULL** prevents these issues.

---

## 🏢 Real-World Examples

### Student Database

Student Name should never be NULL.

### Banking System

Account Number should never be NULL.

### Hospital

Patient ID cannot be NULL.

### E-Commerce

Product Name should always exist.

---

## SQL Syntax

```sql
CREATE TABLE Students(
    Student_ID INT,
    Name VARCHAR(50) NOT NULL,
    Branch VARCHAR(30)
);
```

---

## Insert Example

✅ Valid

```sql
INSERT INTO Students
VALUES (101,'Rahul','CSE');
```

❌ Invalid

```sql
INSERT INTO Students
VALUES (102,NULL,'IT');
```

Database Error

```
ERROR:
Column 'Name' cannot be NULL
```

---

## Internal Working

Whenever INSERT or UPDATE is executed,

Database checks

```
Name == NULL ?

YES
      ↓
Reject Query

NO
      ↓
Store Row
```

---

# 🔑 UNIQUE Constraint

## 📌 What is UNIQUE?

The **UNIQUE** constraint prevents duplicate values.

Every value stored in that column must be different.

---

## Example

Students

| Student_ID | Email |
|------------|----------------|
|101|rahul@gmail.com|
|102|ankit@gmail.com|

Valid

```
abc@gmail.com
xyz@gmail.com
```

Invalid

```
abc@gmail.com
abc@gmail.com
```

---

## SQL Syntax

```sql
CREATE TABLE Students(

Student_ID INT,

Email VARCHAR(100) UNIQUE

);
```

---

## Insert

```sql
INSERT INTO Students
VALUES
(101,'rahul@gmail.com');
```

Works.

---

Second Insert

```sql
INSERT INTO Students
VALUES
(102,'rahul@gmail.com');
```

Error

```
Duplicate entry
```

---

## Internal Working

Database stores an index

```
Email
↓

Index Search

↓

Already Exists ?

YES
↓

Reject

NO
↓

Insert
```

---

## Real World Examples

Email

Passport Number

PAN Number

Driving License Number

Username

All should be UNIQUE.

---

# 👑 PRIMARY KEY Constraint

## 📌 What is PRIMARY KEY?

A Primary Key is a combination of

- UNIQUE
- NOT NULL

It uniquely identifies every row.

There can be **only one Primary Key** per table.

---

## SQL Syntax

```sql
CREATE TABLE Students(

Student_ID INT PRIMARY KEY,

Name VARCHAR(50)

);
```

---

## Example

| Student_ID | Name |
|------------|------|
|101|Rahul|
|102|Ankit|

Valid

---

Invalid

```
Student_ID = 101

Already Exists
```

---

Invalid

```
Student_ID = NULL
```

---

## Rules

✅ Unique

✅ Not NULL

✅ One per table

---

## Internal Working

```
Insert Student_ID

↓

Check NULL ?

↓

Check Duplicate ?

↓

Create Index

↓

Insert Row
```

Primary Keys automatically create an index in most relational databases (such as MySQL, PostgreSQL, and SQL Server), enabling fast lookups.

---

# PRIMARY KEY vs UNIQUE

| Feature | PRIMARY KEY | UNIQUE |
|----------|-------------|---------|
|Duplicate Values|❌ No|❌ No|
|NULL Allowed|❌ No|✅ Usually Yes (DBMS dependent)|
|Only One Per Table|✅ Yes|❌ Multiple|
|Automatically Indexed|✅ Yes|✅ Usually|

---

# PRIMARY KEY vs NOT NULL

| NOT NULL | PRIMARY KEY |
|----------|-------------|
|Prevents NULL|Prevents NULL + Duplicates|
|Many Allowed|Only One Allowed|
|Doesn't Ensure Uniqueness|Ensures Uniqueness|

---

# Common Mistakes

❌ Using Name as Primary Key

```
Rahul
Rahul
Rahul
```

Not unique.

---

❌ Using Phone Number

Phone numbers change.

---

❌ Using Email

Emails can change.

---

✅ Better

```
Student_ID

Employee_ID

Order_ID

Customer_ID
```

---

# FAANG Interview Questions

### Q1. Difference between NULL and Empty String?

NULL means **unknown or missing**.

Empty String means **a known value that happens to contain no characters**.

---

### Q2. Can UNIQUE contain NULL?

Depends on the DBMS.

Most databases allow one or more NULL values because NULL is treated as "unknown."

---

### Q3. Why is PRIMARY KEY faster?

Because it is automatically indexed in most relational databases, enabling efficient lookups.

---

### Q4. Can PRIMARY KEY contain NULL?

No.

---

### Q5. Can a table have two PRIMARY KEYS?

No.

A table can have only one Primary Key, although that Primary Key may consist of multiple columns (Composite Primary Key).

---

# 📝 Practice Questions

1. Create a Students table using NOT NULL and PRIMARY KEY.
2. Add a UNIQUE constraint on Email.
3. Try inserting duplicate Email values.
4. Try inserting NULL into a NOT NULL column.
5. Explain why Student_ID is better than Name as a Primary Key.

---

# 🌐 FOREIGN KEY Constraint

## 📌 What is a FOREIGN KEY?

A **Foreign Key (FK)** is a column (or set of columns) in one table that references the **Primary Key** of another table.

Its main purpose is to **maintain relationships between tables** and enforce **Referential Integrity**.

Simply put,

> A Foreign Key ensures that a child record cannot exist without a valid parent record.

---

# 🤔 Why Do We Need FOREIGN KEY?

Imagine an E-Commerce database.

### Customers Table

| Customer_ID | Name |
|------------|------|
|101|Rahul|
|102|Ankit|

### Orders Table

| Order_ID | Customer_ID |
|----------|-------------|
|1|101|
|2|102|
|3|999|

Question:

Who is Customer 999?

Nobody.

Without a Foreign Key, invalid data can be inserted.

A Foreign Key prevents this.

---

# Parent Table vs Child Table

```
Customers
----------------
Customer_ID (PK)
Name

        ▲
        │
        │
Orders
----------------
Order_ID
Customer_ID (FK)
Amount
```

Customers = Parent Table

Orders = Child Table

---

# SQL Example

### Parent Table

```sql
CREATE TABLE Customers(

Customer_ID INT PRIMARY KEY,

Name VARCHAR(50)

);
```

---

### Child Table

```sql
CREATE TABLE Orders(

Order_ID INT PRIMARY KEY,

Customer_ID INT,

Amount DECIMAL(10,2),

FOREIGN KEY(Customer_ID)
REFERENCES Customers(Customer_ID)

);
```

---

# Valid Insert

Customers

```sql
INSERT INTO Customers
VALUES
(101,'Rahul');
```

Orders

```sql
INSERT INTO Orders
VALUES
(1,101,1500);
```

Works Successfully.

---

# Invalid Insert

```sql
INSERT INTO Orders
VALUES
(2,999,2500);
```

Database Error

```
Cannot add or update child row

Foreign Key Constraint Failed
```

---

# Internal Working

```
Insert Order

↓

Customer_ID = 999

↓

Search Customers Table

↓

Exists ?

YES
     ↓
Insert

NO
     ↓
Reject Query
```

---

# Advantages

✅ Maintains Referential Integrity

✅ Prevents Orphan Records

✅ Maintains Relationships

✅ Improves Database Consistency

---

# Real World Examples

Hospital

```
Patients

↓

Appointments
```

---

College

```
Students

↓

Enrollments
```

---

Amazon

```
Customers

↓

Orders
```

---

Netflix

```
Users

↓

Watch History
```

---

# Referential Integrity

Referential Integrity means

> Every Foreign Key value must match an existing Primary Key value.

Example

Customers

|ID|Name|
|--|----|
|1|Rahul|

Orders

|Order|Customer|
|------|--------|
|100|1|

Valid.

---

Orders

|Order|Customer|
|------|--------|
|101|999|

Invalid.

---

# What Happens When Parent Record is Deleted?

Suppose

Customers

```
101 Rahul
```

Orders

```
Order 1

Customer 101
```

Delete Customer?

What should happen?

There are multiple strategies.

---

# ON DELETE CASCADE

```
Delete Customer

↓

Automatically Delete Orders
```

Example

```sql
FOREIGN KEY(Customer_ID)

REFERENCES Customers(Customer_ID)

ON DELETE CASCADE
```

---

# ON DELETE SET NULL

Delete Parent

↓

Foreign Key becomes NULL

Example

```sql
ON DELETE SET NULL
```

---

# ON DELETE RESTRICT

Default behavior in many databases.

```
Delete Parent

↓

Database Rejects Delete
```

Because child rows still exist.

---

# ON UPDATE CASCADE

Suppose

Customer_ID changes

```
101

↓

105
```

Orders automatically update.

Example

```sql
ON UPDATE CASCADE
```

---

# Common Mistakes

❌ Creating Foreign Key before Parent Table.

---

❌ Different Data Types

```
Parent

INT

Child

VARCHAR
```

Not Allowed.

---

❌ Referencing Non-Primary Columns (unless they are UNIQUE).

---

# ✔ CHECK Constraint

## What is CHECK?

CHECK validates data using a condition.

Only rows satisfying the condition are stored.

---

# SQL Syntax

```sql
CREATE TABLE Employees(

Employee_ID INT PRIMARY KEY,

Age INT CHECK(Age>=18)

);
```

---

# Valid Insert

```sql
INSERT INTO Employees
VALUES

(1,25);
```

Works.

---

# Invalid Insert

```sql
INSERT INTO Employees
VALUES

(2,15);
```

Database Error

```
CHECK Constraint Failed
```

---

# Real World Examples

Age

```
Age >=18
```

---

Salary

```
Salary >0
```

---

Marks

```
Marks BETWEEN 0 AND 100
```

---

Discount

```
Discount <=100
```

---

# Internal Working

```
INSERT

↓

Evaluate Condition

↓

TRUE ?

↓

YES

↓

Insert

NO

↓

Reject
```

---

# ✔ DEFAULT Constraint

## What is DEFAULT?

DEFAULT automatically inserts a value when none is provided.

---

# SQL Example

```sql
CREATE TABLE Students(

Student_ID INT,

Country VARCHAR(30)

DEFAULT 'India'

);
```

---

Insert

```sql
INSERT INTO Students(Student_ID)

VALUES(101);
```

Result

|Student_ID|Country|
|----------|-------|
|101|India|

---

# Benefits

✅ Reduces NULL values

✅ Simplifies INSERT statements

✅ Maintains consistency

---

# Real World Examples

Country

```
India
```

---

Status

```
Active
```

---

Order Status

```
Pending
```

---

Employee Role

```
Intern
```

---

# Practice Questions

1. Create Customers and Orders tables using a Foreign Key.
2. Try inserting an order for a non-existent customer.
3. Create an Employees table with CHECK(Age >= 18).
4. Create a Products table with DEFAULT 'Available' for Status.
5. Explain Referential Integrity with a real-world example.

---
# 🚀 AUTO_INCREMENT Constraint

## 📌 What is AUTO_INCREMENT?

`AUTO_INCREMENT` is a database feature that automatically generates the next unique number whenever a new row is inserted.

It eliminates the need to manually assign IDs.

---

# 🤔 Why Do We Need AUTO_INCREMENT?

Imagine inserting 10 million customers manually.

Without AUTO_INCREMENT

```text
Customer_ID = 101

Customer_ID = 102

Customer_ID = 103
```

The application must calculate the next ID.

Problems:

❌ Duplicate IDs

❌ Human Errors

❌ Race Conditions

❌ Extra Application Logic

AUTO_INCREMENT solves this automatically.

---

# SQL Syntax (MySQL)

```sql
CREATE TABLE Customers(

Customer_ID INT AUTO_INCREMENT PRIMARY KEY,

Customer_Name VARCHAR(50)

);
```

---

# Insert Data

```sql
INSERT INTO Customers(Customer_Name)

VALUES

('Rahul'),

('Ankit'),

('Priya');
```

Result

| Customer_ID | Customer_Name |
|-------------|---------------|
|1|Rahul|
|2|Ankit|
|3|Priya|

Notice that IDs are generated automatically.

---

# How AUTO_INCREMENT Works

```
New Insert

↓

Read Current Counter

↓

Increment Counter

↓

Assign New ID

↓

Store Row
```

---

# Internal Storage

Database maintains

```
AUTO_INCREMENT Counter

↓

Current Value

↓

Next Value
```

Every successful insert updates the counter.

---

# Different Databases

| Database | Feature |
|----------|----------|
|MySQL|AUTO_INCREMENT|
|PostgreSQL|SERIAL / IDENTITY|
|SQL Server|IDENTITY|
|Oracle|SEQUENCE|
|SQLite|AUTOINCREMENT|

---

# Constraint Execution Flow

Whenever a query executes,

the database performs multiple validations.

```
INSERT Query

        │
        ▼

Syntax Validation

        │
        ▼

NOT NULL Check

        │
        ▼

CHECK Constraint

        │
        ▼

UNIQUE Validation

        │
        ▼

PRIMARY KEY Validation

        │
        ▼

FOREIGN KEY Validation

        │
        ▼

DEFAULT Values Applied

        │
        ▼

AUTO_INCREMENT Generated

        │
        ▼

Store Row
```

Only after every validation succeeds is the row written to disk.

---

# Constraint Violations

A violation occurs whenever data breaks one or more constraints.

---

## NOT NULL Violation

```sql
INSERT INTO Students

VALUES

(NULL,'Rahul');
```

Error

```
Column cannot be NULL
```

---

## UNIQUE Violation

```sql
INSERT INTO Students

VALUES

(101,'rahul@gmail.com');

INSERT INTO Students

VALUES

(102,'rahul@gmail.com');
```

Error

```
Duplicate Entry
```

---

## PRIMARY KEY Violation

```sql
INSERT INTO Students

VALUES

(101,'Rahul');

INSERT INTO Students

VALUES

(101,'Ankit');
```

Error

```
Duplicate Primary Key
```

---

## FOREIGN KEY Violation

```sql
INSERT INTO Orders

VALUES

(1,999);
```

Error

```
Foreign Key Constraint Failed
```

---

## CHECK Violation

```sql
Age = -10
```

Error

```
CHECK Constraint Failed
```

---

# Why Constraints Improve Data Quality

Without Constraints

```
Duplicate IDs

↓

Wrong Reports

↓

Incorrect Analytics

↓

Business Loss
```

With Constraints

```
Validated Data

↓

Reliable Reports

↓

Correct Analytics

↓

Better Decisions
```

---

# Common Mistakes

## ❌ Making Everything NOT NULL

Some columns should be optional.

Example

Middle Name

Secondary Phone Number

Profile Picture

---

## ❌ Using Email as PRIMARY KEY

Emails can change.

Use

```
Customer_ID
```

instead.

---

## ❌ Forgetting FOREIGN KEY

This creates orphan records.

Example

Order

↓

No Customer Exists

---

## ❌ No CHECK Constraint

Age

```
-25
```

could be inserted.

---

## ❌ Using Natural Keys Everywhere

Natural Keys

```
Email

Phone

Passport
```

may change.

Enterprise systems usually prefer

```
AUTO_INCREMENT

UUID

IDENTITY
```

---

# Best Practices

✅ Every table should have a PRIMARY KEY.

---

✅ Use FOREIGN KEYS to maintain relationships.

---

✅ Validate business rules using CHECK.

---

✅ Use UNIQUE for Email, Username, Passport Number.

---

✅ Use DEFAULT for frequently repeated values.

---

✅ Prefer Surrogate Keys over Natural Keys in enterprise systems.

---

# Performance Impact

Constraints slightly increase write time because every INSERT and UPDATE must be validated.

However,

they dramatically improve

- Data Quality
- Consistency
- Reliability

which is far more important in production systems.

---

# Interview Tip

A common interview question is:

> Do constraints make queries slower?

Answer:

- **INSERT** and **UPDATE** may be slightly slower because the database performs validation.
- **SELECT** queries are usually unaffected.
- PRIMARY KEY and UNIQUE often improve read performance because they create indexes.

---

# Mini Interview Quiz

### Q1

Why should every production table have a Primary Key?

---

### Q2

Why is Email usually not a good Primary Key?

---

### Q3

Why is CHECK preferred over application validation?

---

### Q4

Why do enterprise systems prefer Surrogate Keys?

---

### Q5

Does FOREIGN KEY improve data integrity?

Explain with an example.

---

# Practice Exercise

Design a database for an Online Shopping System.

Create tables:

- Customers
- Products
- Orders
- Order_Items

Apply appropriate constraints:

- PRIMARY KEY
- FOREIGN KEY
- NOT NULL
- UNIQUE
- CHECK
- DEFAULT

Explain why each constraint is necessary.

---

# 🎯 FAANG SQL Interview Questions

The following are commonly asked SQL Constraint interview questions in companies like Amazon, Google, Microsoft, Meta, Apple, Netflix, Uber, LinkedIn, Walmart Global Tech, Oracle, Adobe, and Salesforce.

---

# 🟢 Beginner Level

### 1. What is a Constraint?

A Constraint is a rule enforced by the database to maintain data accuracy, consistency, and integrity.

---

### 2. Why are Constraints required?

They prevent invalid data from entering the database and ensure reliable relationships between tables.

---

### 3. Name all SQL Constraints.

- NOT NULL
- UNIQUE
- PRIMARY KEY
- FOREIGN KEY
- CHECK
- DEFAULT
- AUTO_INCREMENT (Database Feature)

---

### 4. Difference between PRIMARY KEY and UNIQUE?

| PRIMARY KEY | UNIQUE |
|-------------|---------|
|Only one per table|Multiple allowed|
|Cannot contain NULL|Usually allows NULL|
|Automatically indexed|Usually indexed|

---

### 5. Difference between PRIMARY KEY and FOREIGN KEY?

| PRIMARY KEY | FOREIGN KEY |
|-------------|-------------|
|Uniquely identifies a row|References another table|
|Parent Table|Child Table|
|Unique + NOT NULL|Duplicates Allowed|

---

### 6. Can a table have multiple UNIQUE constraints?

✅ Yes.

---

### 7. Can a table have multiple PRIMARY KEYS?

❌ No.

Only one PRIMARY KEY is allowed.

---

### 8. Can PRIMARY KEY contain NULL?

❌ No.

---

### 9. Can UNIQUE contain NULL?

Depends on DBMS.

Most databases allow NULL because NULL represents an unknown value.

---

### 10. Why is PRIMARY KEY automatically indexed?

To make searching, joining, and sorting much faster.

---

# 🟡 Intermediate Level

### 11. What is Referential Integrity?

It ensures that every Foreign Key value matches an existing Primary Key value.

---

### 12. What happens if a FOREIGN KEY is violated?

The database rejects the INSERT or UPDATE operation.

---

### 13. What is CHECK Constraint?

It validates data using a condition.

Example

```sql
CHECK (Salary > 0)
```

---

### 14. Why is DEFAULT useful?

It automatically inserts a predefined value when no value is provided.

---

### 15. What is AUTO_INCREMENT?

A mechanism that automatically generates sequential IDs.

---

### 16. Why should Email not be a PRIMARY KEY?

Because Email addresses can change.

---

### 17. Why do enterprise databases use Surrogate Keys?

Because they never change and have no business meaning.

---

### 18. Difference between Natural Key and Surrogate Key?

| Natural Key | Surrogate Key |
|--------------|---------------|
|Business Meaning|No Business Meaning|
|Can Change|Never Changes|
|Email, Passport|AUTO_INCREMENT, UUID|

---

### 19. Which Constraint improves data quality the most?

All constraints together maintain data integrity.

---

### 20. Which Constraint prevents duplicate values?

UNIQUE and PRIMARY KEY.

---

# 🔴 Advanced FAANG Questions

### 21. Why are constraints enforced inside the database instead of the application?

Because multiple applications may access the same database.

The database becomes the single source of truth.

---

### 22. Do Constraints reduce INSERT performance?

Slightly.

Every INSERT requires validation.

---

### 23. Do Constraints improve SELECT performance?

PRIMARY KEY and UNIQUE often improve SELECT performance because indexes are created.

---

### 24. What happens internally during INSERT?

```
SQL Parser

↓

Constraint Validation

↓

Index Update

↓

Transaction Log

↓

Storage Engine

↓

Commit
```

---

### 25. What happens if Parent Row is deleted?

Depends on Foreign Key rules.

- CASCADE
- RESTRICT
- SET NULL
- NO ACTION

---

### 26. Difference between CASCADE and RESTRICT?

CASCADE deletes child rows.

RESTRICT prevents parent deletion.

---

### 27. Why is CHECK better than Application Validation?

Database validation works for every application.

Application validation works only inside one application.

---

### 28. Can FOREIGN KEY reference UNIQUE columns?

✅ Yes.

Not only PRIMARY KEY.

---

### 29. Why should every production table have a PRIMARY KEY?

Because it uniquely identifies every row and improves indexing and relationships.

---

### 30. Which Constraint is used most in Enterprise Databases?

- PRIMARY KEY
- FOREIGN KEY
- NOT NULL
- UNIQUE

---

# 🏢 Real Interview Scenario

Design a Hospital Database.

Tables

Patients

Doctors

Appointments

Requirements

- Patient_ID should never repeat.
- Doctor_ID should never repeat.
- Appointment should always belong to an existing Patient.
- Age should be greater than 0.
- Appointment Status should default to "Pending".

Which Constraints will you use?

Answer

| Requirement | Constraint |
|------------|------------|
|Unique Patient|PRIMARY KEY|
|Unique Doctor|PRIMARY KEY|
|Existing Patient|FOREIGN KEY|
|Age > 0|CHECK|
|Status|DEFAULT|

---

# 💻 Hands-on Practice

## Task 1

Create Students Table using

- PRIMARY KEY
- NOT NULL
- UNIQUE

---

## Task 2

Create Customers and Orders tables using FOREIGN KEY.

---

## Task 3

Create Employees table using CHECK.

---

## Task 4

Create Products table using DEFAULT values.

---

## Task 5

Create a complete College Database with appropriate Constraints.

---

# 📚 Assignment

Design a database for an Online Food Delivery System.

Create the following tables:

- Customers
- Restaurants
- Delivery Partners
- Orders
- Payments

Apply all suitable Constraints.

Explain why each Constraint is required.

---

# 📝 Chapter Summary

## We Learned

✅ Why Constraints are needed

✅ NOT NULL

✅ UNIQUE

✅ PRIMARY KEY

✅ FOREIGN KEY

✅ CHECK

✅ DEFAULT

✅ AUTO_INCREMENT

✅ Referential Integrity

✅ Constraint Violations

✅ Internal Working

✅ Performance Impact

✅ Best Practices

✅ FAANG Interview Questions

---

# 🧠 Revision Mind Map

```
                     SQL CONSTRAINTS
                           │
 ┌─────────────────────────┼─────────────────────────┐
 │                         │                         │
NOT NULL               UNIQUE                 PRIMARY KEY
 │                         │                         │
No NULL             No Duplicates      Unique + NOT NULL
                           │
                     FOREIGN KEY
                           │
                Referential Integrity
                           │
        ┌──────────────┬──────────────┐
        │              │              │
      CHECK         DEFAULT     AUTO_INCREMENT
        │              │              │
 Business Rules   Default Value   Auto IDs
```





