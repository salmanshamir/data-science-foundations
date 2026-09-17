# DML and Data Retrieval Fundamentals

## 1. What is DML?

**DML** stands for **Data Manipulation Language**.

DML is used to work with the **actual data/records stored inside tables**.

The main DML commands are:

    INSERT
    UPDATE
    DELETE

These commands manipulate the data without changing the basic structure of the table.

### DDL vs DML

DDL works mainly with the **structure**:

    DDL
    │
    ├── CREATE
    ├── ALTER
    ├── DROP
    └── TRUNCATE

DML works mainly with the **data**:

    DML
    │
    ├── INSERT
    ├── UPDATE
    └── DELETE

There is another important command:

    SELECT

`SELECT` is generally classified as **DQL (Data Query Language)** rather than DML because it is used to retrieve/query data.

For learning purposes, we will study `INSERT`, `UPDATE`, `DELETE`, and `SELECT` together because they are the fundamental commands for working with table data.

---

# 2. INSERT

`INSERT` is used to **add new records/rows to a table**.

Suppose we have:

    students

    id | name | age
    ---|------|----

We can insert a record:

    INSERT INTO students
    VALUES (1, 'Salman', 25);

The table becomes:

    id | name   | age
    ---|--------|----
    1  | Salman | 25

---

## 2.1 INSERT with Column Names

A better approach is to specify the column names.

    INSERT INTO students (id, name, age)
    VALUES (1, 'Salman', 25);

This explicitly tells SQL which value belongs to which column.

### Why is this recommended?

Suppose the table contains:

    id
    name
    age
    city

You may only want to provide:

    id
    name
    age

You can write:

    INSERT INTO students (id, name, age)
    VALUES (1, 'Salman', 25);

If `city` allows `NULL` or has a default value, SQL can handle the missing column accordingly.

---

# 3. INSERT Multiple Rows

Multiple rows can be inserted using one `INSERT` statement.

    INSERT INTO students (id, name, age)
    VALUES
        (1, 'Salman', 25),
        (2, 'Ahmed', 23),
        (3, 'Ali', 27);

Result:

    id | name   | age
    ---|--------|----
    1  | Salman | 25
    2  | Ahmed  | 23
    3  | Ali    | 27

---

# 4. INSERT and NULL

`NULL` means that a value is **missing, unknown, or not provided**.

Suppose:

    students

    id | name   | age | city
    ---|--------|-----|------

We can insert:

    INSERT INTO students (id, name, age)
    VALUES (1, 'Salman', 25);

If `city` allows `NULL`, the result can be:

    id | name   | age | city
    ---|--------|-----|------
    1  | Salman | 25  | NULL

Remember:

    NULL

is different from:

    ''

An empty string is a value.

`NULL` represents the absence of a value.

---

# 5. INSERT and DEFAULT

A column can have a default value.

For example:

    CREATE TABLE employees (
        id INT,
        name VARCHAR(100),
        country VARCHAR(50) DEFAULT 'Pakistan'
    );

Now we can insert:

    INSERT INTO employees (id, name)
    VALUES (1, 'Salman');

The database automatically uses:

    country = Pakistan

because `Pakistan` was defined as the default value.

---

# 6. UPDATE

`UPDATE` is used to **modify existing records**.

Suppose we have:

    students

    id | name   | age
    ---|--------|----
    1  | Salman | 25
    2  | Ahmed  | 23
    3  | Ali    | 27

Suppose Salman is now 26.

We can write:

    UPDATE students
    SET age = 26
    WHERE id = 1;

Result:

    id | name   | age
    ---|--------|----
    1  | Salman | 26
    2  | Ahmed  | 23
    3  | Ali    | 27

---

# 7. UPDATE Multiple Columns

You can update more than one column at the same time.

    UPDATE students
    SET name = 'Muhammad Salman',
        age = 26
    WHERE id = 1;

Both values are changed for the selected record.

---

# 8. The Importance of WHERE with UPDATE

This is extremely important.

Consider:

    UPDATE students
    SET age = 30;

There is no `WHERE` clause.

Therefore, **all rows can be updated**.

Before:

    id | name   | age
    ---|--------|----
    1  | Salman | 25
    2  | Ahmed  | 23
    3  | Ali    | 27

After:

    id | name   | age
    ---|--------|----
    1  | Salman | 30
    2  | Ahmed  | 30
    3  | Ali    | 30

Therefore, always carefully consider the `WHERE` condition when using `UPDATE`.

---

# 9. DELETE

`DELETE` is used to **remove records/rows from a table**.

Example:

    DELETE FROM students
    WHERE id = 3;

Before:

    id | name
    ---|-------
    1  | Salman
    2  | Ahmed
    3  | Ali

After:

    id | name
    ---|-------
    1  | Salman
    2  | Ahmed

The row where `id = 3` has been removed.

---

# 10. DELETE Without WHERE

Be very careful with:

    DELETE FROM students;

There is no `WHERE` clause.

Therefore, all rows are deleted.

However, the table itself remains.

Compare:

    DROP
    ↓
    Removes the table itself

    TRUNCATE
    ↓
    Removes all rows while keeping the table structure

    DELETE
    ↓
    Removes rows

`DELETE` can also be used with a `WHERE` clause to remove selected records.

---

# 11. INSERT vs UPDATE vs DELETE

| Command | Purpose |
|---|---|
| `INSERT` | Adds new rows |
| `UPDATE` | Modifies existing rows |
| `DELETE` | Removes existing rows |

Easy way to remember:

    INSERT → Add
    UPDATE → Change
    DELETE → Remove

---

# 12. SELECT

`SELECT` is used to **retrieve/read/query data** from a database.

It is generally considered part of **DQL — Data Query Language**.

Suppose:

    employees

    id | name   | department | salary
    ---|--------|------------|-------
    1  | Salman | IT         | 50000
    2  | Ahmed  | HR         | 45000
    3  | Ali    | IT         | 60000

To retrieve everything:

    SELECT *
    FROM employees;

The `*` means:

> Select all columns.

---

# 13. SELECT Specific Columns

Instead of selecting every column, we can select only the columns we need.

    SELECT name, salary
    FROM employees;

Result:

    name   | salary
    -------|-------
    Salman | 50000
    Ahmed  | 45000
    Ali    | 60000

This is often better than using `SELECT *` when you only need specific information.

---

# 14. SELECT with Expressions

SQL can perform calculations while retrieving data.

For example:

    SELECT salary,
           salary * 12 AS annual_salary
    FROM employees;

Result:

    salary | annual_salary
    -------|--------------
    50000  | 600000
    45000  | 540000
    60000  | 720000

Here:

    salary * 12

is an expression.

And:

    AS annual_salary

gives the calculated column a temporary name.

---

# 15. Alias

An **alias** is a temporary name given to a column or table.

Example:

    SELECT name AS employee_name
    FROM employees;

Instead of:

    name

the result displays:

    employee_name

Another example:

    SELECT salary * 12 AS annual_salary
    FROM employees;

Aliases are particularly useful when working with calculated values.

---

# 16. WHERE Clause

The `WHERE` clause is used to **filter rows based on a condition**.

Suppose:

    employees

    id | name   | department | salary
    ---|--------|------------|-------
    1  | Salman | IT         | 50000
    2  | Ahmed  | HR         | 45000
    3  | Ali    | IT         | 60000

Suppose we want employees whose salary is greater than 50,000.

    SELECT *
    FROM employees
    WHERE salary > 50000;

Result:

    id | name | department | salary
    ---|------|------------|-------
    3  | Ali  | IT         | 60000

So:

    WHERE
    ↓
    Filters rows

---

# 17. WHERE with Text

We can also filter text values.

    SELECT *
    FROM employees
    WHERE department = 'IT';

Result:

    id | name   | department | salary
    ---|--------|------------|-------
    1  | Salman | IT         | 50000
    3  | Ali    | IT         | 60000

Text values are normally written inside quotes.

---

# 18. Comparison Operators

The most important comparison operators are:

| Operator | Meaning |
|---|---|
| `=` | Equal to |
| `!=` | Not equal to |
| `<>` | Not equal to |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal to |
| `<=` | Less than or equal to |

Examples:

    WHERE salary = 50000

    WHERE salary > 50000

    WHERE salary >= 50000

    WHERE salary < 50000

    WHERE salary <= 50000

    WHERE salary <> 50000

---

# 19. AND

`AND` is used when **all conditions must be true**.

Example:

    SELECT *
    FROM employees
    WHERE department = 'IT'
    AND salary > 50000;

The employee must satisfy both conditions:

    department = IT
    AND
    salary > 50000

---

# 20. OR

`OR` is used when **at least one condition must be true**.

    SELECT *
    FROM employees
    WHERE department = 'IT'
    OR department = 'HR';

An employee can satisfy either condition.

---

# 21. NOT

`NOT` reverses a condition.

    SELECT *
    FROM employees
    WHERE NOT department = 'IT';

This returns employees who are not in the IT department.

---

# 22. BETWEEN

`BETWEEN` checks whether a value falls within a range.

    SELECT *
    FROM employees
    WHERE salary BETWEEN 40000 AND 60000;

This normally includes both boundary values:

    40000 <= salary <= 60000

---

# 23. IN

`IN` is useful when checking multiple possible values.

Instead of:

    WHERE department = 'IT'
    OR department = 'HR'
    OR department = 'Sales'

we can write:

    WHERE department IN ('IT', 'HR', 'Sales');

This is shorter and easier to read.

---

# 24. LIKE

`LIKE` is used for **pattern matching**.

Suppose we have:

    name
    ---------
    Salman
    Ahmed
    Ali
    Sami

## Names starting with S

    SELECT *
    FROM employees
    WHERE name LIKE 'S%';

`%` represents zero or more characters.

This can match:

    Salman
    Sami

## Names ending with n

    WHERE name LIKE '%n';

## Names containing "al"

    WHERE name LIKE '%al%';

---

# 25. NULL Conditions

You should not check for `NULL` using:

    WHERE salary = NULL;

Instead use:

    WHERE salary IS NULL;

To find values that are not NULL:

    WHERE salary IS NOT NULL;

Remember:

    NULL

is not the same as:

    0

and it is not the same as:

    ''

`NULL` represents the absence of a value.

---

# 26. Scalar Operations

A **scalar operation** works with individual values and normally produces a result for each row.

For example:

    SELECT salary * 12
    FROM employees;

Suppose we have:

    salary
    -------
    50000
    45000
    60000

The calculation is performed separately:

    50000 × 12 → 600000
    45000 × 12 → 540000
    60000 × 12 → 720000

Therefore:

    Scalar operation
    ↓
    Row-level calculation

---

# 27. Arithmetic Operators

SQL supports common arithmetic operators.

    +
    -
    *
    /
    %

### Addition

    SELECT salary + 5000
    FROM employees;

### Subtraction

    SELECT salary - 5000
    FROM employees;

### Multiplication

    SELECT salary * 12
    FROM employees;

### Division

    SELECT salary / 12
    FROM employees;

### Modulus

    SELECT salary % 2
    FROM employees;

The `%` operator returns the remainder.

For example:

    10 % 3 = 1

---

# 28. Scalar Functions

A **scalar function** accepts a value and returns a value for each row.

Some common scalar functions are:

    UPPER()
    LOWER()
    LENGTH()
    ROUND()
    ABS()

---

# 29. UPPER()

Converts text to uppercase.

    SELECT UPPER(name)
    FROM employees;

Example:

    Salman

becomes:

    SALMAN

---

# 30. LOWER()

Converts text to lowercase.

    SELECT LOWER(name)
    FROM employees;

Example:

    Salman

becomes:

    salman

---

# 31. LENGTH()

Returns the length of a string.

    SELECT name, LENGTH(name)
    FROM employees;

For example:

    Salman

contains 6 characters.

Result:

    name   | LENGTH(name)
    -------|-------------
    Salman | 6

---

# 32. ROUND()

`ROUND()` is used to round a numeric value.

    SELECT ROUND(1234.5678, 2);

Result:

    1234.57

The second argument specifies the number of decimal places.

---

# 33. ABS()

`ABS()` returns the absolute value.

    SELECT ABS(-500);

Result:

    500

Another example:

    SELECT ABS(500);

Result:

    500

---

# 34. Scalar vs Aggregate Operations

This distinction is very important.

## Scalar

Works independently on each row.

    SELECT salary * 12
    FROM employees;

If there are 100 employees:

    100 rows
    ↓
    100 calculations
    ↓
    100 results

## Aggregate

Works across multiple rows and produces a summary.

    SELECT AVG(salary)
    FROM employees;

If there are 100 employees:

    100 salaries
    ↓
    AVG()
    ↓
    1 result

Therefore:

    Scalar
    ↓
    Row-level calculation

    Aggregate
    ↓
    Multiple rows → summary result

---

# 35. Aggregate Operations

Aggregate functions are used to **summarize data from multiple rows**.

The most important aggregate functions are:

    COUNT()
    SUM()
    AVG()
    MIN()
    MAX()

These are extremely important for data analysis.

---

# 36. COUNT()

`COUNT()` is used to count rows or non-NULL values.

## COUNT(*)

    SELECT COUNT(*)
    FROM employees;

If the table contains 100 employees:

    100

`COUNT(*)` counts rows.

## COUNT(column)

    SELECT COUNT(salary)
    FROM employees;

This counts the **non-NULL values** in the `salary` column.

Important difference:

    COUNT(*)
    ↓
    Counts rows

    COUNT(column)
    ↓
    Counts non-NULL values in that column

---

# 37. SUM()

`SUM()` calculates the total of numeric values.

    SELECT SUM(salary)
    FROM employees;

Suppose:

    50000
    45000
    60000

Then:

    SUM = 155000

---

# 38. AVG()

`AVG()` calculates the average.

    SELECT AVG(salary)
    FROM employees;

For:

    50000
    45000
    60000

the result is:

    51666.67

---

# 39. MIN()

`MIN()` returns the smallest value.

    SELECT MIN(salary)
    FROM employees;

For:

    50000
    45000
    60000

the result is:

    45000

---

# 40. MAX()

`MAX()` returns the largest value.

    SELECT MAX(salary)
    FROM employees;

For:

    50000
    45000
    60000

the result is:

    60000

---

# 41. Aggregate Functions Summary

| Function | Purpose |
|---|---|
| `COUNT()` | Counts |
| `SUM()` | Calculates total |
| `AVG()` | Calculates average |
| `MIN()` | Finds minimum |
| `MAX()` | Finds maximum |

Easy way to remember:

    COUNT → How many?

    SUM → What is the total?

    AVG → What is the average?

    MIN → What is the smallest?

    MAX → What is the largest?

---

# 42. Scalar vs Aggregate — Final Comparison

| Scalar | Aggregate |
|---|---|
| Works row by row | Works across multiple rows |
| Produces a value for each row | Usually produces a summary |
| Row-level calculation | Multiple-row calculation |
| `salary * 12` | `SUM(salary)` |
| `UPPER(name)` | `AVG(salary)` |
| `ROUND(salary, 2)` | `MAX(salary)` |

Example:

    SELECT name, salary * 12
    FROM employees;

This produces a result for each employee.

Whereas:

    SELECT AVG(salary)
    FROM employees;

produces one overall average.

---

# 43. SQL Query Execution Order

This is a **very important concept**.

The order in which we **write** a SQL query is not exactly the same as the conceptual order in which SQL processes it.

For example:

    SELECT name, salary
    FROM employees
    WHERE salary > 50000;

We write:

    SELECT
    FROM
    WHERE

But conceptually SQL processes the query approximately as:

    FROM
     ↓
    WHERE
     ↓
    SELECT

---

# 44. Basic Query Execution Order

For the concepts learned so far, remember:

    1. FROM
          ↓
    2. WHERE
          ↓
    3. SELECT

### FROM

First, SQL identifies the table/data source.

    FROM employees

### WHERE

Then SQL filters the rows.

    WHERE salary > 50000

### SELECT

Finally, SQL determines what columns/expressions should appear in the result.

    SELECT name, salary

---

# 45. Query Execution Order with Aggregate Functions

Suppose:

    SELECT AVG(salary)
    FROM employees
    WHERE department = 'IT';

Conceptually:

    FROM
     ↓
    WHERE
     ↓
    Aggregate calculation
     ↓
    SELECT

### Step 1 — FROM

Get data from:

    employees

### Step 2 — WHERE

Filter:

    department = 'IT'

### Step 3 — Aggregate Calculation

Calculate:

    AVG(salary)

### Step 4 — SELECT

Return the result.

---

# 46. Written Order vs Execution Order

### Written order

    SELECT
    FROM
    WHERE

### Conceptual execution order

    FROM
    ↓
    WHERE
    ↓
    SELECT

This difference becomes very important as SQL queries become more advanced.

---

# 47. Query Execution Order — Future Reference

As more SQL clauses are learned, the conceptual order becomes larger.

A commonly used conceptual sequence is:

    FROM
    ↓
    WHERE
    ↓
    GROUP BY
    ↓
    HAVING
    ↓
    SELECT
    ↓
    ORDER BY
    ↓
    LIMIT

**For now, do not worry about `GROUP BY`, `HAVING`, or `LIMIT`.**

They will be learned later.

For the current topic, focus on:

    FROM
    ↓
    WHERE
    ↓
    SELECT

---

# 48. Complete Example

Consider this table:

    employees

    id | name   | department | salary
    ---|--------|------------|-------
    1  | Salman | IT         | 50000
    2  | Ali    | IT         | 60000
    3  | Ahmed  | HR         | 45000
    4  | Hamza  | HR         | 55000
    5  | Usman  | Sales      | 70000

### Show all employees

    SELECT *
    FROM employees;

### Show only names and salaries

    SELECT name, salary
    FROM employees;

### Find employees earning more than 50,000

    SELECT *
    FROM employees
    WHERE salary > 50000;

### Calculate annual salary

    SELECT name,
           salary * 12 AS annual_salary
    FROM employees;

### Convert names to uppercase

    SELECT UPPER(name) AS name
    FROM employees;

### Find total salary

    SELECT SUM(salary) AS total_salary
    FROM employees;

### Find average salary

    SELECT AVG(salary) AS average_salary
    FROM employees;

### Find minimum salary

    SELECT MIN(salary) AS minimum_salary
    FROM employees;

### Find maximum salary

    SELECT MAX(salary) AS maximum_salary
    FROM employees;

### Count employees

    SELECT COUNT(*) AS total_employees
    FROM employees;

### Find IT employees earning more than 50,000

    SELECT name, salary
    FROM employees
    WHERE department = 'IT'
    AND salary > 50000;

---

# 49. Important Safety Rule

Be especially careful with:

    UPDATE
    DELETE

Always think about the `WHERE` clause.

For example:

    UPDATE employees
    SET salary = 60000;

can affect every row.

And:

    DELETE FROM employees;

can delete every row.

Before executing an `UPDATE` or `DELETE`, it is often useful to first check the target rows using:

    SELECT *
    FROM employees
    WHERE ...;

For example:

    SELECT *
    FROM employees
    WHERE department = 'IT';

Then, if those are the rows you actually intend to modify:

    UPDATE employees
    SET salary = salary + 5000
    WHERE department = 'IT';

This is a very good habit when working with real databases.

---

# 50. What You Should Know After This Topic

You should now understand:

## DML

    INSERT
    UPDATE
    DELETE

## DQL

    SELECT

## Filtering

    WHERE
    AND
    OR
    NOT
    IN
    BETWEEN
    LIKE
    IS NULL
    IS NOT NULL

## Comparison Operators

    =
    !=
    <>
    >
    <
    >=
    <=

## Arithmetic Operators

    +
    -
    *
    /
    %

## Scalar Functions

    UPPER()
    LOWER()
    LENGTH()
    ROUND()
    ABS()

## Aggregate Functions

    COUNT()
    SUM()
    AVG()
    MIN()
    MAX()

## Query Execution Order

For the current concepts:

    FROM
    ↓
    WHERE
    ↓
    SELECT

And remember the central distinction:

    INSERT
    → Add data

    UPDATE
    → Change data

    DELETE
    → Remove data

    SELECT
    → Retrieve data

    WHERE
    → Filter rows

    Scalar
    → Row-by-row calculation

    Aggregate
    → Summary calculation

---
