# SQL — DDL (Data Definition Language)

## 1. What is SQL?

**SQL (Structured Query Language)** is a language used to communicate with and work with relational databases.

SQL can be used to:

* Create databases and tables
* Define the structure of tables
* Insert data
* Retrieve data
* Update data
* Delete data
* Modify database structures
* Apply rules and relationships to data

SQL is divided into different categories of commands. The first category we are learning is **DDL**.

---

# 2. What is DDL?

**DDL stands for Data Definition Language.**

DDL is used to **define, create, modify, and remove the structure of database objects**.

Database objects include:

* Databases
* Tables
* Columns
* Constraints
* Views and other database objects

### Simple definition

> **DDL is used to define and manage the structure of a database.**

DDL mainly deals with the **structure**, rather than working directly with individual records.

---

# 3. DDL Commands

The main DDL commands we need to learn are:

| Command    | Purpose                                               |
| ---------- | ----------------------------------------------------- |
| `CREATE`   | Creates a new database object                         |
| `ALTER`    | Changes the structure of an existing object           |
| `DROP`     | Permanently removes an object                         |
| `TRUNCATE` | Removes all records while keeping the table structure |

The basic idea is:

```text
CREATE     → Build something
ALTER      → Change its structure
TRUNCATE   → Empty it
DROP       → Remove it completely
```

---

# 4. CREATE

## What is CREATE?

`CREATE` is used to create a new database object.

We commonly use it to create:

* Database
* Table

---

## 4.1 CREATE DATABASE

### Syntax

```sql
CREATE DATABASE database_name;
```

### Example

```sql
CREATE DATABASE company;
```

This creates a database named `company`.

To use the database:

```sql
USE company;
```

---

## 4.2 CREATE TABLE

`CREATE TABLE` is used to create a new table.

### Syntax

```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    column3 datatype
);
```

### Example

```sql
CREATE TABLE employees (
    employee_id INT,
    name VARCHAR(100),
    age INT,
    salary DECIMAL(10,2)
);
```

This creates a table named `employees` with four columns:

```text
employees
│
├── employee_id
├── name
├── age
└── salary
```

---

# 5. SQL Data Types

When creating a table, every column normally needs a data type.

## Numeric Data Types

```sql
INT
BIGINT
FLOAT
DOUBLE
DECIMAL
```

Example:

```sql
age INT
```

```sql
salary DECIMAL(10,2)
```

`DECIMAL(10,2)` means:

* Maximum 10 digits in total
* 2 digits after the decimal point

Example:

```text
25000.50
1500.75
```

---

## String/Text Data Types

```sql
CHAR
VARCHAR
TEXT
```

Example:

```sql
name VARCHAR(100)
```

`VARCHAR(100)` means the column can store variable-length text up to 100 characters.

---

## Date and Time Data Types

```sql
DATE
TIME
DATETIME
TIMESTAMP
```

Example:

```sql
birth_date DATE
```

A `DATE` value can look like:

```text
2000-05-21
```

---

# 6. CREATE TABLE with Constraints

When creating a table, we can also define **constraints**.

Example:

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE,
    age INT CHECK (age >= 18),
    salary DECIMAL(10,2) DEFAULT 30000
);
```

Here we used:

* `PRIMARY KEY`
* `NOT NULL`
* `UNIQUE`
* `CHECK`
* `DEFAULT`

We will understand these constraints in detail later.

---

# 7. ALTER

## What is ALTER?

`ALTER` is used to **change the structure of an existing table**.

Using `ALTER TABLE`, we can:

* Add a new column
* Modify an existing column
* Rename a column
* Drop a column
* Add constraints
* Drop constraints

### Simple definition

> **ALTER changes the structure of an existing table without creating a new table.**

Suppose we have:

```sql
CREATE TABLE employees (
    employee_id INT,
    name VARCHAR(100),
    salary DECIMAL(10,2)
);
```

Later, we decide that we also need an email column.

Instead of deleting the table and creating it again, we can use `ALTER`.

---

# 8. ALTER — ADD COLUMN

## Purpose

Adds a new column to an existing table.

### Syntax

```sql
ALTER TABLE table_name
ADD COLUMN column_name datatype;
```

### Example

```sql
ALTER TABLE employees
ADD COLUMN email VARCHAR(150);
```

Before:

```text
employee_id
name
salary
```

After:

```text
employee_id
name
salary
email
```

We can also add multiple columns:

```sql
ALTER TABLE employees
ADD COLUMN department VARCHAR(100),
ADD COLUMN phone VARCHAR(20);
```

---

# 9. ALTER — MODIFY COLUMN

## Purpose

Changes the definition of an existing column.

For example, suppose we have:

```sql
name VARCHAR(50)
```

and we want to allow up to 100 characters.

In MySQL:

```sql
ALTER TABLE employees
MODIFY COLUMN name VARCHAR(100);
```

The column changes from:

```text
VARCHAR(50)
```

to:

```text
VARCHAR(100)
```

We can also change other properties such as data type or constraints, but we must be careful because changing a column's type can affect existing data.

---

# 10. ALTER — RENAME COLUMN

## Purpose

Changes the name of an existing column.

### Syntax

```sql
ALTER TABLE table_name
RENAME COLUMN old_name TO new_name;
```

### Example

```sql
ALTER TABLE employees
RENAME COLUMN name TO full_name;
```

Before:

```text
name
```

After:

```text
full_name
```

The data inside the column remains; only the column name changes.

---

# 11. ALTER — DROP COLUMN

## Purpose

Removes a column from an existing table.

### Syntax

```sql
ALTER TABLE table_name
DROP COLUMN column_name;
```

### Example

```sql
ALTER TABLE employees
DROP COLUMN email;
```

The `email` column and the data stored in that column are removed.

### Important

Dropping a column is destructive.

Before doing it, make sure the column is no longer needed.

---

# 12. ALTER — RENAME TABLE

`ALTER` can also be used to rename an existing table.

### Syntax

```sql
ALTER TABLE old_table_name
RENAME TO new_table_name;
```

### Example

```sql
ALTER TABLE employees
RENAME TO staff;
```

The table name changes:

```text
employees
    ↓
staff
```

The data and columns remain.

---

# 13. ALTER — ADD CONSTRAINT

Constraints can also be added after a table has already been created.

For example:

```sql
ALTER TABLE employees
ADD CONSTRAINT pk_employee
PRIMARY KEY (employee_id);
```

Another example:

```sql
ALTER TABLE employees
ADD CONSTRAINT unique_email
UNIQUE (email);
```

We can therefore use `ALTER` to modify both the **columns** and the **rules** of a table.

---

# 14. DROP

## What is DROP?

`DROP` permanently removes a database object.

It can be used to remove:

* Database
* Table
* Other database objects

### Simple definition

> **DROP removes the object itself, including its structure and data.**

---

# 15. DROP TABLE

### Syntax

```sql
DROP TABLE table_name;
```

### Example

```sql
DROP TABLE employees;
```

After this command:

* The table no longer exists
* All rows are removed
* All columns are removed
* The table structure is removed
* Constraints belonging to the table are removed

For example:

```text
employees
    ↓
DROP TABLE
    ↓
Table no longer exists
```

If we try:

```sql
SELECT * FROM employees;
```

after dropping the table, the query will fail because the table does not exist.

---

# 16. DROP DATABASE

### Syntax

```sql
DROP DATABASE database_name;
```

### Example

```sql
DROP DATABASE company;
```

This removes the database and its objects.

### WARNING

`DROP DATABASE` is a destructive operation.

Always make sure you are working with the correct database before using it.

---

# 17. DROP vs ALTER

These commands do very different things.

```text
ALTER
  ↓
Change the structure
  ↓
Object still exists
```

Whereas:

```text
DROP
  ↓
Remove the object
  ↓
Object no longer exists
```

Example:

```sql
ALTER TABLE employees
ADD COLUMN email VARCHAR(100);
```

The table still exists.

But:

```sql
DROP TABLE employees;
```

removes the table completely.

---

# 18. TRUNCATE

## What is TRUNCATE?

`TRUNCATE` removes **all rows from a table while keeping the table structure**.

### Syntax

```sql
TRUNCATE TABLE table_name;
```

### Example

```sql
TRUNCATE TABLE employees;
```

Suppose the table contains:

```text
employee_id | name
------------|--------
1           | Salman
2           | Ahmed
3           | Ali
```

After:

```sql
TRUNCATE TABLE employees;
```

the rows are gone:

```text
employee_id | name
------------|--------
(empty)
```

But the table itself still exists.

The columns still exist, and the table can be used again.

---

# 19. TRUNCATE vs DROP

This is one of the most important differences to remember.

| DROP                    | TRUNCATE                     |
| ----------------------- | ---------------------------- |
| Removes the table       | Removes all rows             |
| Structure is removed    | Structure remains            |
| Data is removed         | Data is removed              |
| Table no longer exists  | Table still exists           |
| Must recreate the table | Can continue using the table |

Think:

```text
DROP
↓
Table + Structure + Data
        ↓
      GONE
```

Whereas:

```text
TRUNCATE
↓
Data
 ↓
GONE

Table + Structure
 ↓
REMAINS
```

---

# 20. DELETE vs TRUNCATE

`DELETE` is not a DDL command; it belongs to DML.

We will study it later, but the basic difference is important.

### DELETE

```sql
DELETE FROM employees;
```

Can remove rows from a table.

It can also use a `WHERE` condition:

```sql
DELETE FROM employees
WHERE employee_id = 5;
```

This removes only the employee with ID 5.

### TRUNCATE

```sql
TRUNCATE TABLE employees;
```

Removes all rows from the table.

It does not use a `WHERE` clause.

---

# 21. DDL Summary

The four main DDL commands can be remembered like this:

| Command    | What it does                           |
| ---------- | -------------------------------------- |
| `CREATE`   | Creates an object                      |
| `ALTER`    | Changes an existing object's structure |
| `TRUNCATE` | Removes all rows but keeps the table   |
| `DROP`     | Removes the object completely          |

A simple memory trick:

```text
CREATE   → Create
ALTER    → Change
TRUNCATE → Empty
DROP     → Destroy
```

---

# 22. What are Constraints?

A **constraint** is a rule applied to a column or table to control what kind of data can be stored.

### Simple definition

> **Constraints protect the accuracy, consistency, and integrity of data.**

For example, suppose we have an employee table.

We don't want two employees to have the same employee ID.

We can use:

```sql
employee_id INT PRIMARY KEY
```

Now the database itself enforces this rule.

---

# 23. Main SQL Constraints

The major constraints you should learn are:

```text
PRIMARY KEY
FOREIGN KEY
NOT NULL
UNIQUE
CHECK
DEFAULT
```

---

# 24. PRIMARY KEY

## What is a Primary Key?

A primary key is used to **uniquely identify each row in a table**.

Example:

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100),
    age INT
);
```

Here:

```sql
student_id INT PRIMARY KEY
```

means every student must have a unique ID.

Valid:

```text
student_id | name
-----------|--------
1          | Salman
2          | Ahmed
3          | Ali
```

Invalid:

```text
student_id | name
-----------|--------
1          | Salman
1          | Ahmed
```

because two rows cannot have the same primary key.

A primary key also cannot contain `NULL`.

---

# 25. Characteristics of PRIMARY KEY

A primary key:

* Uniquely identifies a row
* Cannot contain duplicate values
* Cannot contain `NULL`
* There is normally one primary key definition per table
* Can contain one column
* Can also contain multiple columns

When multiple columns together form a primary key, it is called a **composite primary key**.

Example:

```sql
PRIMARY KEY (student_id, course_id)
```

---

# 26. NOT NULL

## What is NOT NULL?

`NOT NULL` means a column cannot contain `NULL`.

Example:

```sql
CREATE TABLE employees (
    employee_id INT,
    name VARCHAR(100) NOT NULL
);
```

The `name` column must have a value.

This is not allowed:

```text
employee_id | name
------------|------
1           | NULL
```

But this is allowed:

```text
employee_id | name
------------|--------
1           | Salman
```

---

# 27. What is NULL?

`NULL` means that a value is missing, unknown, or not provided.

`NULL` is different from:

```text
0
```

and different from:

```text
''
```

For example:

```text
salary = NULL
```

means the salary value is not available.

Whereas:

```text
salary = 0
```

means the salary is explicitly zero.

---

# 28. UNIQUE

## What is UNIQUE?

`UNIQUE` prevents duplicate values in a column.

Example:

```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    email VARCHAR(150) UNIQUE
);
```

This is valid:

```text
user_id | email
--------|----------------
1       | a@gmail.com
2       | b@gmail.com
```

But this violates the uniqueness rule:

```text
user_id | email
--------|----------------
1       | a@gmail.com
2       | a@gmail.com
```

because the same email appears twice.

---

# 29. PRIMARY KEY vs UNIQUE

| PRIMARY KEY                          | UNIQUE                                                |
| ------------------------------------ | ----------------------------------------------------- |
| Identifies a row                     | Prevents duplicate values                             |
| Cannot be `NULL`                     | Can generally allow `NULL` depending on DBMS behavior |
| One primary key definition per table | Multiple UNIQUE constraints can exist                 |
| Main identifier                      | Additional uniqueness rule                            |

Example:

```sql
employee_id INT PRIMARY KEY,
email VARCHAR(150) UNIQUE
```

Here:

* `employee_id` identifies the employee.
* `email` must also be unique.

---

# 30. CHECK

## What is CHECK?

`CHECK` ensures that a value satisfies a specified condition.

Example:

```sql
age INT CHECK (age >= 18)
```

This means the age must be 18 or greater.

Valid:

```text
age = 25
```

Invalid:

```text
age = 15
```

Another example:

```sql
salary DECIMAL(10,2)
CHECK (salary > 0)
```

The salary must be greater than zero.

We can also restrict values:

```sql
gender VARCHAR(10)
CHECK (gender IN ('Male', 'Female', 'Other'))
```

---

# 31. DEFAULT

## What is DEFAULT?

`DEFAULT` automatically provides a value when no value is supplied for that column during insertion.

Example:

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    country VARCHAR(50) DEFAULT 'Pakistan'
);
```

If we insert:

```sql
INSERT INTO employees (employee_id, name)
VALUES (1, 'Salman');
```

We did not provide a country.

The database can use the default value:

```text
country = Pakistan
```

---

# 32. FOREIGN KEY

## What is a Foreign Key?

A foreign key creates a **relationship between tables**.

Suppose we have two tables:

```text
customers
orders
```

A customer can have multiple orders.

### Customers table

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100)
);
```

### Orders table

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    amount DECIMAL(10,2),

    FOREIGN KEY (customer_id)
        REFERENCES customers(customer_id)
);
```

Here:

```text
customers
    |
    | customer_id
    ↓
orders
```

`customers.customer_id` is the primary key.

`orders.customer_id` is the foreign key.

---

# 33. Why FOREIGN KEY is important

Suppose the customers table contains:

```text
customer_id
-----------
1
2
3
```

Then an order can reference:

```text
customer_id = 1
```

because customer 1 exists.

But if we try to create an order with:

```text
customer_id = 99
```

and customer 99 does not exist, the foreign key can prevent the invalid relationship.

This protects **referential integrity**.

---

# 34. Primary Key and Foreign Key Relationship

Think about it like this:

```text
CUSTOMERS
-----------------
customer_id  PK
name
       |
       |
       ↓
ORDERS
-----------------
order_id     PK
customer_id  FK
amount
```

The primary key identifies the record in the parent table.

The foreign key references that record from another table.

This concept will become extremely important when we study **JOINs**.

---

# 35. Complete Example with Constraints

Here is a practical example using multiple constraints:

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE,
    age INT CHECK (age >= 18),
    country VARCHAR(50) DEFAULT 'Pakistan'
);
```

This table has:

```text
customer_id → PRIMARY KEY
name        → NOT NULL
email       → UNIQUE
age         → CHECK
country     → DEFAULT
```

Now create the orders table:

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT NOT NULL,
    amount DECIMAL(10,2) CHECK (amount > 0),

    FOREIGN KEY (customer_id)
        REFERENCES customers(customer_id)
);
```

Here:

```text
order_id    → PRIMARY KEY
customer_id → NOT NULL + FOREIGN KEY
amount      → CHECK
```

---

# 36. DDL in a Real Workflow

A typical database design process might look like:

```text
1. CREATE DATABASE
        ↓
2. USE DATABASE
        ↓
3. CREATE TABLE
        ↓
4. Define columns and data types
        ↓
5. Add constraints
        ↓
6. ALTER table when requirements change
        ↓
7. TRUNCATE when all records need to be removed
        ↓
8. DROP when the object is no longer required
```

Example:

```sql
CREATE DATABASE shop;

USE shop;

CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE
);

ALTER TABLE customers
ADD COLUMN phone VARCHAR(20);
```

---

# 37. Important Differences

## CREATE vs ALTER

```text
CREATE
→ Creates a new object

ALTER
→ Changes an existing object
```

---

## ALTER vs DROP

```text
ALTER
→ Changes the structure
→ Object remains

DROP
→ Removes the object
→ Object no longer exists
```

---

## DROP vs TRUNCATE

```text
DROP
→ Removes table + structure + data

TRUNCATE
→ Removes all data
→ Keeps table structure
```

---

## TRUNCATE vs DELETE

```text
TRUNCATE
→ DDL
→ Removes all rows
→ No WHERE condition

DELETE
→ DML
→ Can remove selected rows
→ Can use WHERE
```

---

# 38. DDL Cheat Sheet

```sql
-- Create database
CREATE DATABASE database_name;

-- Select database
USE database_name;

-- Create table
CREATE TABLE table_name (
    column_name datatype
);

-- Add column
ALTER TABLE table_name
ADD COLUMN column_name datatype;

-- Modify column
ALTER TABLE table_name
MODIFY COLUMN column_name datatype;

-- Rename column
ALTER TABLE table_name
RENAME COLUMN old_name TO new_name;

-- Drop column
ALTER TABLE table_name
DROP COLUMN column_name;

-- Rename table
ALTER TABLE old_table_name
RENAME TO new_table_name;

-- Drop table
DROP TABLE table_name;

-- Drop database
DROP DATABASE database_name;

-- Remove all rows while keeping structure
TRUNCATE TABLE table_name;
```

---

# 39. Constraint Cheat Sheet

```sql
-- Primary key
column_name INT PRIMARY KEY

-- Not null
column_name VARCHAR(100) NOT NULL

-- Unique
column_name VARCHAR(100) UNIQUE

-- Check
age INT CHECK (age >= 18)

-- Default
country VARCHAR(50) DEFAULT 'Pakistan'

-- Foreign key
FOREIGN KEY (customer_id)
REFERENCES customers(customer_id)
```

---

# 40. Final Concept Map

```text
SQL
│
├── DDL
│   │
│   ├── CREATE
│   │   ├── CREATE DATABASE
│   │   └── CREATE TABLE
│   │
│   ├── ALTER
│   │   ├── ADD COLUMN
│   │   ├── MODIFY COLUMN
│   │   ├── RENAME COLUMN
│   │   ├── DROP COLUMN
│   │   ├── RENAME TABLE
│   │   └── ADD/DROP CONSTRAINT
│   │
│   ├── TRUNCATE
│   │   └── Remove all rows
│   │
│   └── DROP
│       ├── DROP TABLE
│       └── DROP DATABASE
│
└── CONSTRAINTS
    │
    ├── PRIMARY KEY
    ├── FOREIGN KEY
    ├── NOT NULL
    ├── UNIQUE
    ├── CHECK
    └── DEFAULT
```
