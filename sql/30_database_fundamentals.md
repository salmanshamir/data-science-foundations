# Database Fundamentals

## 1. What is a Database?

A **database** is an organized collection of data that is stored in a way that makes it easy to:

* Store data
* Find data
* Update data
* Delete data
* Manage large amounts of information
* Maintain relationships between different pieces of data

### Simple Definition

> A database is an organized place where data is stored and managed electronically.

For example, imagine a university.

It may need to store information about:

* Students
* Teachers
* Courses
* Departments
* Exams
* Results

Instead of keeping all this information in separate text files, a database can organize and manage it efficiently.

---

# 2. Real-World Example of a Database

Suppose we have an online shopping system.

The system may need to store:

```text
Customers
Products
Orders
Payments
Employees
Categories
```

A database could organize this information into different tables:

```text
Database: OnlineShop

├── customers
├── products
├── orders
├── order_details
├── payments
└── categories
```

These tables can also be related to each other.

For example:

```text
Customer
   ↓
Order
   ↓
Order Details
   ↓
Product
```

This is one of the main ideas behind relational databases.

---

# 3. Why Do We Need Databases?

Without a proper database system, managing large amounts of data becomes difficult.

Imagine storing customer information in thousands of separate files.

You would have problems such as:

* Duplicate data
* Difficult searching
* Difficult updating
* Data inconsistency
* Security problems
* Difficult relationships between data
* Difficulty handling many users at the same time

Databases solve many of these problems.

---

# 4. Database vs Data

These two terms are different.

## Data

**Data** is the individual information we store.

For example:

```text
Salman
25
Pakistan
50000
```

These are pieces of data.

## Database

A **database** is an organized collection of related data.

For example:

```text
Customers

customer_id | name   | age | country
------------|--------|-----|--------
1           | Salman | 25  | Pakistan
2           | Ahmed  | 30  | Pakistan
3           | Ali    | 28  | Pakistan
```

The complete collection is the database data organized into a structure.

---

# 5. Types of Databases

There are several types of databases.

Some important types are:

1. Relational Database
2. NoSQL Database
3. Hierarchical Database
4. Network Database
5. Object-Oriented Database
6. Distributed Database

For SQL and Data Analysis, the most important one to understand first is the **Relational Database**.

---

# 6. Relational Database

A **Relational Database** stores data in tables and allows relationships to be created between those tables.

Examples of relational database systems include:

* MySQL
* PostgreSQL
* Microsoft SQL Server
* Oracle Database
* SQLite

A relational database usually contains:

```text
Database
   ↓
Tables
   ↓
Rows + Columns
   ↓
Relationships
```

---

# 7. Why is it called "Relational"?

The word **relational** comes from the concept of a **relation**.

In relational database theory, a relation represents a table.

For example:

```text
students

student_id | name   | age
-----------|--------|----
1          | Salman | 25
2          | Ahmed  | 23
3          | Ali    | 27
```

This table is a relation.

Different relations/tables can also be connected through keys.

For example:

```text
students
   |
   | student_id
   ↓
enrollments
   |
   | course_id
   ↓
courses
```

---

# 8. DBMS

## What is DBMS?

**DBMS stands for Database Management System.**

A DBMS is software that allows us to create, store, manage, retrieve, update, and control data in databases.

### Simple Definition

> A DBMS is software that helps us manage databases.

The database is the **data and structure**.

The DBMS is the **software that manages it**.

---

# 9. Examples of DBMS

Examples include:

```text
MySQL
PostgreSQL
Oracle Database
Microsoft SQL Server
SQLite
MongoDB
```

However, not all of these are relational database systems.

For example:

```text
MySQL              → Relational
PostgreSQL         → Relational
Oracle             → Relational
SQL Server         → Relational
SQLite             → Relational
MongoDB            → NoSQL / Document Database
```

---

# 10. What Does a DBMS Do?

A DBMS provides many functions.

It allows us to:

### 1. Create databases

```sql
CREATE DATABASE shop;
```

### 2. Create tables

```sql
CREATE TABLE customers (...);
```

### 3. Insert data

```sql
INSERT INTO customers (...);
```

### 4. Retrieve data

```sql
SELECT * FROM customers;
```

### 5. Update data

```sql
UPDATE customers
SET name = 'Salman'
WHERE customer_id = 1;
```

### 6. Delete data

```sql
DELETE FROM customers
WHERE customer_id = 1;
```

### 7. Control access

A DBMS can control which users are allowed to access particular databases or operations.

### 8. Maintain data integrity

Constraints and other mechanisms help ensure that invalid data is not stored.

### 9. Handle multiple users

A DBMS can manage situations where multiple users or applications access the database simultaneously.

---

# 11. RDBMS

## What is RDBMS?

**RDBMS stands for Relational Database Management System.**

An RDBMS is a DBMS specifically designed to manage **relational databases**.

### Simple Definition

> An RDBMS is software used to manage data stored in related tables.

Examples:

```text
MySQL
PostgreSQL
Oracle Database
Microsoft SQL Server
SQLite
```

These are commonly used relational database management systems.

---

# 12. DBMS vs RDBMS

The easiest way to understand the relationship is:

```text
DBMS
│
└── Database Management System
        │
        └── RDBMS
              │
              └── Relational Database Management System
```

An RDBMS is a type of DBMS designed around the relational model.

### Simple comparison

| DBMS                                  | RDBMS                                        |
| ------------------------------------- | -------------------------------------------- |
| General database management system    | Relational database management system        |
| May use different data models         | Uses relational model                        |
| Relationships may not be central      | Relationships between tables are fundamental |
| Can refer to broader database systems | Specifically manages relational databases    |

For your SQL learning, **RDBMS is especially important** because MySQL is an RDBMS.

---

# 13. Database vs DBMS vs RDBMS

These terms are often confused.

### Database

The actual organized collection of data.

```text
customers
products
orders
```

### DBMS

The software used to manage databases.

```text
Database
      ↑
     DBMS
```

### RDBMS

A type of DBMS designed for relational databases.

```text
RDBMS
 ↓
Relational Database
 ↓
Tables + Relationships
```

### Example

If you use MySQL:

```text
MySQL
  ↓
RDBMS
  ↓
Manages relational databases
  ↓
Database
  ↓
Tables
  ↓
Rows + Columns
```

---

# 14. Database Terminology

Before learning SQL deeply, we need to understand some important database terminology.

The most important terms are:

```text
Database
Table
Relation
Row
Tuple
Column
Attribute
Domain
Cardinality
Degree
Primary Key
Foreign Key
```

---

# 15. Table

A **table** is a structured collection of related data organized into rows and columns.

Example:

```text
students

student_id | name   | age
-----------|--------|----
1          | Salman | 25
2          | Ahmed  | 23
3          | Ali    | 27
```

Here:

```text
Table = students
```

---

# 16. Relation

In the relational database model, a **relation** represents a table.

For example:

```text
students

student_id | name   | age
-----------|--------|----
1          | Salman | 25
2          | Ahmed  | 23
3          | Ali    | 27
```

This table can be considered a **relation**.

### Easy way to remember

```text
Relational Database
        ↓
Relation
        ↓
Table
```

In practical SQL discussions, people often simply say "table" rather than "relation."

---

# 17. Row

A **row** represents one complete record in a table.

Example:

```text
student_id | name   | age
-----------|--------|----
1          | Salman | 25
```

The entire line:

```text
1 | Salman | 25
```

is one row/record.

Another row:

```text
2 | Ahmed | 23
```

represents another student.

---

# 18. Tuple

In relational database terminology, a **row is called a tuple**.

For example:

```text
student_id | name   | age
-----------|--------|----
1          | Salman | 25
```

The tuple is:

```text
(1, Salman, 25)
```

So:

```text
Row = Tuple
```

The word **record** is also commonly used to describe a row.

Therefore:

```text
Row = Record = Tuple
```

They are closely related terms referring to one complete entry in a relation.

---

# 19. Column

A **column** represents a particular type/category of information.

Example:

```text
student_id | name   | age
-----------|--------|----
1          | Salman | 25
2          | Ahmed  | 23
3          | Ali    | 27
```

The columns are:

```text
student_id
name
age
```

Each column describes one property of the records.

---

# 20. Attribute

In relational database terminology, a **column is called an attribute**.

For example:

```text
student_id | name | age
```

The attributes are:

```text
student_id
name
age
```

Therefore:

```text
Column = Attribute
```

### Easy distinction

```text
Rows    → Records / Tuples
Columns → Attributes
```

---

# 21. Domain

A **domain** is the set of valid values that an attribute can contain.

For example, suppose:

```text
age
```

is an attribute.

Its domain could be:

```text
Integer values from 0 to 120
```

For a country attribute:

```text
country
```

the domain could be a set of valid country names.

In SQL, data types and constraints help define what values are acceptable for a column.

Example:

```sql
age INT CHECK (age >= 0);
```

This restricts the possible values.

---

# 22. Degree

**Degree** refers to the number of attributes/columns in a relation.

Example:

```text
students

student_id | name | age | country
```

There are four columns:

```text
student_id
name
age
country
```

Therefore:

```text
Degree = 4
```

### Simple formula

```text
Degree = Number of Columns
```

---

# 23. Cardinality

**Cardinality** refers to the number of rows/tuples in a relation.

Example:

```text
students

student_id | name
-----------|--------
1          | Salman
2          | Ahmed
3          | Ali
4          | Hamza
```

There are 4 rows.

Therefore:

```text
Cardinality = 4
```

### Simple formula

```text
Cardinality = Number of Rows
```

---

# 24. Degree vs Cardinality

This is very important.

Suppose we have:

```text
students

student_id | name   | age | country
-----------|--------|-----|--------
1          | Salman | 25  | Pakistan
2          | Ahmed  | 23  | Pakistan
3          | Ali    | 27  | Pakistan
```

There are:

```text
4 columns
3 rows
```

Therefore:

```text
Degree = 4
Cardinality = 3
```

### Remember

```text
DEGREE
↓
Columns

CARDINALITY
↓
Rows
```

A useful memory trick:

> **Degree → Describes the columns**

> **Cardinality → Counts the records**

---

# 25. Complete Terminology Example

Consider this table:

```text
employees

employee_id | name   | department | salary
------------|--------|------------|--------
1           | Salman | IT         | 50000
2           | Ahmed  | HR         | 45000
3           | Ali    | IT         | 60000
```

Now identify everything.

### Database

The database might be:

```text
company
```

### Table

```text
employees
```

### Relation

```text
employees relation
```

### Attributes / Columns

```text
employee_id
name
department
salary
```

### Tuples / Rows

```text
(1, Salman, IT, 50000)
(2, Ahmed, HR, 45000)
(3, Ali, IT, 60000)
```

### Degree

There are 4 columns.

```text
Degree = 4
```

### Cardinality

There are 3 rows.

```text
Cardinality = 3
```

---

# 26. Keys

Keys are extremely important in relational databases.

A key is used to identify records or create relationships between tables.

Important keys include:

```text
Primary Key
Foreign Key
Candidate Key
Alternate Key
Composite Key
```

For your initial SQL learning, **Primary Key and Foreign Key are the most important**.

---

# 27. Primary Key

A primary key uniquely identifies each record.

Example:

```text
student_id | name
-----------|--------
1          | Salman
2          | Ahmed
3          | Ali
```

Here:

```text
student_id
```

can be the primary key.

It must be:

* Unique
* Not NULL

Example:

```sql
student_id INT PRIMARY KEY
```

---

# 28. Foreign Key

A foreign key is used to create a relationship between tables.

Example:

```text
customers

customer_id | name
------------|--------
1           | Salman
2           | Ahmed
```

And:

```text
orders

order_id | customer_id
---------|------------
101      | 1
102      | 2
103      | 1
```

Here:

```text
customers.customer_id
```

is the primary key.

And:

```text
orders.customer_id
```

is the foreign key.

The relationship is:

```text
customers
    |
    | customer_id
    ↓
orders
```

---

# 29. Relationships Between Tables

Relational databases become powerful because tables can be related to each other.

Common relationship types are:

```text
One-to-One
One-to-Many
Many-to-Many
```

---

# 30. One-to-One Relationship

One record in Table A is related to one record in Table B.

Example:

```text
Person
   |
   | 1 : 1
   ↓
Passport
```

One person has one passport, and one passport belongs to one person.

Conceptually:

```text
Person 1 ───── 1 Passport
```

---

# 31. One-to-Many Relationship

One record in Table A can be related to many records in Table B.

This is very common.

Example:

```text
Customer
   |
   | 1 : Many
   ↓
Orders
```

One customer can make many orders.

For example:

```text
Customer 1
    ↓
Order 101
Order 102
Order 103
```

So:

```text
Customer 1 ─────< Orders
```

---

# 32. Many-to-Many Relationship

Many records in Table A can be related to many records in Table B.

Example:

```text
Students
    ↕
Courses
```

One student can enroll in many courses.

One course can have many students.

Therefore:

```text
Students
    ↕
Many-to-Many
    ↕
Courses
```

In a relational database, a many-to-many relationship is normally implemented using a **junction/bridge table**.

For example:

```text
students
courses
enrollments
```

The `enrollments` table connects the two.

```text
students
    ↓
enrollments
    ↓
courses
```

---

# 33. Database Schema

A **database schema** describes the structure/design of a database.

It can include:

* Tables
* Columns
* Data types
* Relationships
* Constraints
* Keys

For example:

```text
Company Database

customers
├── customer_id
├── name
└── email

orders
├── order_id
├── customer_id
└── amount

Relationship:
customers.customer_id
        ↓
orders.customer_id
```

This overall design is part of the database schema.

---

# 34. Data Integrity

**Data integrity** means maintaining the accuracy, consistency, and reliability of data.

For example, if an order refers to customer ID 50, but customer 50 doesn't exist, we have an invalid relationship.

A foreign key can help prevent this situation.

Similarly:

```sql
email VARCHAR(150) UNIQUE
```

can help prevent duplicate emails.

And:

```sql
age INT CHECK (age >= 18)
```

can prevent invalid ages.

Therefore, constraints help maintain data integrity.

---

# 35. Database Terminology Cheat Sheet

| Term        | Meaning                                                  |
| ----------- | -------------------------------------------------------- |
| Database    | Organized collection of data                             |
| DBMS        | Software used to manage databases                        |
| RDBMS       | DBMS based on the relational model                       |
| Table       | Collection of related data organized in rows and columns |
| Relation    | A table in the relational model                          |
| Row         | One complete record                                      |
| Record      | Another common term for a row                            |
| Tuple       | Relational term for a row                                |
| Column      | A category/type of information                           |
| Attribute   | Relational term for a column                             |
| Domain      | Set of valid values for an attribute                     |
| Degree      | Number of columns                                        |
| Cardinality | Number of rows                                           |
| Primary Key | Uniquely identifies a row                                |
| Foreign Key | References a key in another table                        |
| Schema      | Structure/design of a database                           |
| Constraint  | Rule that controls allowed data                          |

---

# 36. Important Concept to Remember

Consider:

```text
Database
    ↓
Table / Relation
    ↓
 ┌───────────────┐
 │   Attributes  │ → Columns
 ├───────────────┤
 │    Tuples     │ → Rows
 └───────────────┘
```

And:

```text
Degree
   ↓
Number of Columns

Cardinality
   ↓
Number of Rows
```

---

# 37. DBMS vs RDBMS — Simple Example

Imagine a library.

The **database** contains information about:

```text
Books
Members
Loans
Authors
```

The **DBMS/RDBMS** is the software that manages this information.

For an RDBMS, the information could be organized as:

```text
Library Database
│
├── books
│
├── members
│
├── authors
│
└── loans
```

And the tables can have relationships:

```text
members
    |
    ↓
loans
    |
    ↓
books
```

SQL is the language we use to communicate with the RDBMS.

```text
You
 ↓
SQL
 ↓
RDBMS
 ↓
Database
 ↓
Tables
 ↓
Data
```

---

# 38. Database Fundamentals — Final Concept Map

```text
DATABASE FUNDAMENTALS
│
├── Database
│   └── Organized collection of data
│
├── Database Types
│   ├── Relational
│   ├── NoSQL
│   ├── Hierarchical
│   ├── Network
│   └── Object-Oriented
│
├── DBMS
│   └── Software that manages databases
│
├── RDBMS
│   └── DBMS based on relational model
│
├── Relational Database
│   ├── Tables
│   ├── Rows
│   ├── Columns
│   └── Relationships
│
├── Terminology
│   ├── Relation → Table
│   ├── Tuple → Row
│   ├── Attribute → Column
│   ├── Domain → Valid values
│   ├── Degree → Number of columns
│   └── Cardinality → Number of rows
│
├── Keys
│   ├── Primary Key
│   └── Foreign Key
│
├── Relationships
│   ├── One-to-One
│   ├── One-to-Many
│   └── Many-to-Many
│
└── Schema
    └── Structure/design of database
```
