# 🔑 SQL Journey – Chapter 2: Keys (FAANG Level)

> **Goal:** Master SQL Keys from Basic → FAANG Interview Level by understanding **why they exist, how they work internally, and when to use each one.**

---

# 📚 Table of Contents

- Why Do We Need Keys?
- What is a Key?
- Characteristics of a Good Key
- Types of Keys
  - Super Key
  - Candidate Key
  - Primary Key
  - Alternate Key
  - Composite Key
  - Foreign Key
  - Natural Key
  - Surrogate Key
  - Unique Key
- Relationship Between Keys
- Internal Working of Keys
- Keys vs Indexes
- SQL Examples
- Real-World Examples
- FAANG Interview Questions
- Chapter Summary

---

# ❓ Why Do We Need Keys?

Imagine the following table.

| Name | Branch | Age |
|------|--------|-----|
| Rahul | CSE | 21 |
| Rahul | IT | 22 |
| Rahul | AI | 20 |

Question:

> Which Rahul is the correct student?

We don't know.

Without keys:

- ❌ Duplicate records
- ❌ Wrong updates
- ❌ Wrong deletions
- ❌ Impossible relationships
- ❌ Poor data integrity

**Keys solve these problems by uniquely identifying every row.**

---

# 🔑 What is a Key?

A **Key** is one or more columns used to:

- Uniquely identify a row
- Create relationships between tables
- Prevent duplicate records
- Maintain data integrity

Example

| Student_ID | Name | Branch |
|------------|------|--------|
|101|Rahul|CSE|
|102|Ankit|IT|
|103|Priya|ECE|

Here,

**Student_ID** is the Key.

---

# ⭐ Characteristics of a Good Key

A good key should be:

- ✅ Unique
- ✅ Stable
- ✅ Minimal
- ✅ Never NULL (for Primary Key)
- ✅ Easy to reference

---

# 🏆 Types of Keys

---

# 1️⃣ Super Key

A **Super Key** is **any combination of one or more columns that uniquely identifies a row.**

It **may contain unnecessary attributes.**

### Examples

✅ Student_ID

✅ Email

✅ Student_ID + Name

✅ Student_ID + Email

Not Super Keys

❌ Name

❌ Branch

---

# 2️⃣ Candidate Key

A **Candidate Key** is a **minimal Super Key**.

It contains **no unnecessary attributes.**

Example

| Student_ID | Email |
|------------|--------|

Both uniquely identify rows.

Therefore,

Candidate Keys

- Student_ID
- Email

Not Candidate Key

Student_ID + Name

Because Name is unnecessary.

---

# 3️⃣ Primary Key

A **Primary Key** is the Candidate Key selected to uniquely identify every row.

Rules

- Must be Unique
- Cannot be NULL
- Only One Primary Key per table

Example

```sql
CREATE TABLE Students(
    Student_ID INT PRIMARY KEY,
    Name VARCHAR(50)
);
```

---

# 4️⃣ Alternate Key

Candidate Keys **not selected** as the Primary Key.

Example

Candidate Keys

- Student_ID
- Email

Primary Key

- Student_ID

Alternate Key

- Email

---

# 5️⃣ Composite Key

A key consisting of **two or more columns.**

Example

Order Details

| Order_ID | Product_ID |
|----------|------------|

Neither column alone is unique.

Together,

(Order_ID, Product_ID)

forms a Composite Key.

Example

```sql
PRIMARY KEY (Order_ID, Product_ID)
```

---

# 6️⃣ Foreign Key

A **Foreign Key** creates relationships between tables.

Parent Table

Students

| Student_ID | Name |
|------------|------|
|101|Rahul|

Child Table

Enrollments

| Enrollment_ID | Student_ID |
|---------------|------------|
|1|101|

Student_ID in Enrollments references Students(Student_ID).

Example

```sql
FOREIGN KEY(Student_ID)
REFERENCES Students(Student_ID)
```

---

# 7️⃣ Natural Key

A Natural Key already exists in the real world.

Examples

- Passport Number
- Aadhaar Number
- Email

Advantages

- Meaningful

Disadvantages

- May change

---

# 8️⃣ Surrogate Key

A system-generated key with **no business meaning**.

Examples

- AUTO_INCREMENT
- IDENTITY
- UUID

Advantages

- Stable
- Never changes
- Preferred in enterprise databases

---

# 9️⃣ Unique Key

Ensures unique values.

Difference from Primary Key

| Primary Key | Unique Key |
|--------------|------------|
|Cannot be NULL|Usually allows NULL (DBMS dependent)|
|One per table|Multiple allowed|

Example

```sql
Email VARCHAR(100) UNIQUE
```

---

# 🔄 Relationship Between Keys

```
                    Super Keys
                         │
        ┌────────────────┴──────────────┐
        │                               │
 Candidate Keys                 Super Keys with
 (Minimal)                      Extra Columns
        │
        │
 Primary Key
        │
 Alternate Keys
```

---

# ⚙️ Internal Working of Keys

Database Engine

```
User Query
      │
      ▼
Primary Key Lookup
      │
      ▼
Index Search
      │
      ▼
Matching Row
```

Keys are usually backed by **Indexes**, allowing databases to retrieve rows quickly.

---

# 🔗 Keys vs Indexes

| Keys | Indexes |
|------|----------|
|Maintain uniqueness|Improve speed|
|Logical concept|Physical data structure|
|Ensure integrity|Optimize queries|

---

# 💻 SQL Examples

### Create Table

```sql
CREATE TABLE Students(
    Student_ID INT PRIMARY KEY,
    Email VARCHAR(100) UNIQUE,
    Name VARCHAR(50),
    Branch VARCHAR(20)
);
```

---

### Parent Table

```sql
CREATE TABLE Students(
    Student_ID INT PRIMARY KEY,
    Name VARCHAR(50)
);
```

---

### Child Table

```sql
CREATE TABLE Enrollments(
    Enrollment_ID INT PRIMARY KEY,
    Student_ID INT,
    FOREIGN KEY(Student_ID)
    REFERENCES Students(Student_ID)
);
```

---

# 🌍 Real-World Examples

| Entity | Key |
|---------|-----|
|Student|Student_ID|
|Employee|Employee_ID|
|Order|Order_ID|
|Customer|Customer_ID|
|Product|Product_ID|
|Passport|Passport_Number|

---

# 🎯 FAANG Interview Questions

### Basic

1. What is a Key?
2. Why do we need Keys?
3. Difference between Super Key and Candidate Key?
4. Difference between Candidate Key and Primary Key?
5. Difference between Primary Key and Unique Key?

### Intermediate

6. Can a table have multiple Candidate Keys?
7. Can a Primary Key contain NULL?
8. Why is only one Primary Key allowed?
9. Why is a Composite Key used?
10. Why is a Foreign Key important?

### Advanced (FAANG)

11. Why are Surrogate Keys preferred in enterprise systems?
12. How do Keys improve Join performance?
13. What happens if a Primary Key is updated?
14. How does the Query Optimizer use Primary Keys?
15. Difference between Logical Keys and Physical Indexes?

---

# 📝 Practice Questions

1. Identify the Candidate Key.
2. Find the Super Keys.
3. Design a Composite Key for an Order table.
4. Create Parent and Child tables using Foreign Keys.
5. Compare Natural and Surrogate Keys.

---

# 🚀 Chapter Summary

✅ Keys uniquely identify rows.

✅ Super Key = Unique + May contain extra attributes.

✅ Candidate Key = Minimal Super Key.

✅ Primary Key = Selected Candidate Key.

✅ Alternate Key = Remaining Candidate Keys.

✅ Composite Key = Combination of multiple columns.

✅ Foreign Key = Creates relationships.

✅ Natural Key = Real-world identifier.

✅ Surrogate Key = System-generated identifier.

✅ Unique Key = Prevents duplicate values.

---

# 📖 Next Chapter

## Chapter 3 – Constraints (FAANG Level)

Topics Covered

- NOT NULL
- UNIQUE
- PRIMARY KEY
- FOREIGN KEY
- CHECK
- DEFAULT
- Internal Working
- SQL Examples
- 30+ FAANG Interview Questions
- Practice Problems

---

⭐ **Mastering Keys is the foundation for understanding Joins, Normalization, Indexing, Transactions, and Database Design in FAANG interviews.**
