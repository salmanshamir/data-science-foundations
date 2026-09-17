# ORDER BY, GROUP BY and HAVING in SQL

## 1. ORDER BY

`ORDER BY` is used to sort the rows returned by a SQL query.

Suppose we have an `employees` table:

| id | name | department | salary |
|---:|---|---|---:|
| 1 | Salman | IT | 50000 |
| 2 | Ahmed | HR | 70000 |
| 3 | Ali | IT | 40000 |
| 4 | Hamza | Sales | 60000 |

If we write:

    SELECT *
    FROM employees;

SQL does not guarantee that the result will appear in a particular order.

If we want to sort the employees by salary:

    SELECT *
    FROM employees
    ORDER BY salary;

The result will normally be:

| id | name | department | salary |
|---:|---|---|---:|
| 3 | Ali | IT | 40000 |
| 1 | Salman | IT | 50000 |
| 4 | Hamza | Sales | 60000 |
| 2 | Ahmed | HR | 70000 |

By default, `ORDER BY` uses ascending order.

---

# 2. ASC

`ASC` means **ascending order**.

    SELECT *
    FROM employees
    ORDER BY salary ASC;

Result:

| salary |
|---:|
| 40000 |
| 50000 |
| 60000 |
| 70000 |

Ascending generally means:

    Small → Large
    A → Z
    Old → New

These two queries are normally equivalent:

    ORDER BY salary;

    ORDER BY salary ASC;

---

# 3. DESC

`DESC` means **descending order**.

    SELECT *
    FROM employees
    ORDER BY salary DESC;

Result:

| salary |
|---:|
| 70000 |
| 60000 |
| 50000 |
| 40000 |

Descending generally means:

    Large → Small
    Z → A
    New → Old

---

# 4. ORDER BY Text

`ORDER BY` can also sort text columns.

    SELECT *
    FROM employees
    ORDER BY name ASC;

This sorts names alphabetically:

    Ahmed
    Ali
    Hamza
    Salman

Descending:

    SELECT *
    FROM employees
    ORDER BY name DESC;

Result:

    Salman
    Hamza
    Ali
    Ahmed

---

# 5. ORDER BY Multiple Columns

You can sort using more than one column.

Suppose:

| name | department | salary |
|---|---|---:|
| Salman | IT | 50000 |
| Ali | IT | 60000 |
| Ahmed | HR | 50000 |
| Hamza | HR | 60000 |

Query:

    SELECT *
    FROM employees
    ORDER BY department ASC, salary DESC;

SQL first sorts by `department`.

Then, if two or more rows have the same department, SQL sorts those rows by `salary DESC`.

Result:

| name | department | salary |
|---|---|---:|
| Hamza | HR | 60000 |
| Ahmed | HR | 50000 |
| Ali | IT | 60000 |
| Salman | IT | 50000 |

Think of it as:

    First priority  → department
    Second priority → salary

---

# 6. ORDER BY with WHERE

`WHERE` filters rows first, and `ORDER BY` sorts the remaining rows.

    SELECT *
    FROM employees
    WHERE department = 'IT'
    ORDER BY salary DESC;

Conceptually:

    employees
        ↓
    WHERE department = 'IT'
        ↓
    remaining IT employees
        ↓
    ORDER BY salary DESC
        ↓
    final sorted result

---

# 7. ORDER BY with Calculated Values

You can also sort using a calculated value.

For example, if `salary` is monthly salary:

    SELECT name,
           salary * 12 AS annual_salary
    FROM employees
    ORDER BY annual_salary DESC;

Here:

    salary * 12

calculates annual salary.

Then:

    ORDER BY annual_salary DESC

sorts employees from highest annual salary to lowest.

---

# 8. GROUP BY

`GROUP BY` is used to create groups of rows that have the same value in one or more columns.

Suppose we have:

| id | name | department | salary |
|---:|---|---|---:|
| 1 | Salman | IT | 50000 |
| 2 | Ali | IT | 60000 |
| 3 | Ahmed | HR | 45000 |
| 4 | Hamza | HR | 55000 |
| 5 | Usman | Sales | 70000 |

There are three departments:

    IT
    HR
    Sales

If we write:

    SELECT department
    FROM employees
    GROUP BY department;

SQL creates groups based on the department.

Result:

| department |
|---|
| IT |
| HR |
| Sales |

The important idea is:

    GROUP BY
    ↓
    Rows → Groups

---

# 9. Why Do We Need GROUP BY?

`GROUP BY` becomes especially useful when combined with aggregate functions.

Suppose we want:

> What is the average salary of each department?

If we write:

    SELECT AVG(salary)
    FROM employees;

we get the average salary of all employees together.

But we want the average salary separately for each department.

Therefore:

    SELECT department,
           AVG(salary) AS average_salary
    FROM employees
    GROUP BY department;

Result:

| department | average_salary |
|---|---:|
| IT | 55000 |
| HR | 50000 |
| Sales | 70000 |

Here SQL:

1. Creates a group for IT.
2. Creates a group for HR.
3. Creates a group for Sales.
4. Calculates the average salary separately for each group.

---

# 10. GROUP BY with COUNT()

`COUNT()` counts rows or values.

Example:

    SELECT department,
           COUNT(*) AS total_employees
    FROM employees
    GROUP BY department;

Result:

| department | total_employees |
|---|---:|
| IT | 2 |
| HR | 2 |
| Sales | 1 |

Meaning:

    IT → 2 employees
    HR → 2 employees
    Sales → 1 employee

---

# 11. GROUP BY with SUM()

`SUM()` calculates the total of numeric values.

Example:

    SELECT department,
           SUM(salary) AS total_salary
    FROM employees
    GROUP BY department;

Result:

| department | total_salary |
|---|---:|
| IT | 110000 |
| HR | 100000 |
| Sales | 70000 |

The salary is added separately for every department.

---

# 12. GROUP BY with AVG()

`AVG()` calculates the average.

    SELECT department,
           AVG(salary) AS average_salary
    FROM employees
    GROUP BY department;

Result:

| department | average_salary |
|---|---:|
| IT | 55000 |
| HR | 50000 |
| Sales | 70000 |

---

# 13. GROUP BY with MIN()

`MIN()` finds the smallest value in each group.

    SELECT department,
           MIN(salary) AS minimum_salary
    FROM employees
    GROUP BY department;

Meaning:

    Find the lowest salary in every department.

---

# 14. GROUP BY with MAX()

`MAX()` finds the largest value in each group.

    SELECT department,
           MAX(salary) AS maximum_salary
    FROM employees
    GROUP BY department;

Meaning:

    Find the highest salary in every department.

---

# 15. GROUP BY with Multiple Columns

You can group by more than one column.

Suppose:

| department | gender | salary |
|---|---|---:|
| IT | Male | 50000 |
| IT | Female | 60000 |
| IT | Male | 55000 |
| HR | Male | 45000 |
| HR | Female | 50000 |

Query:

    SELECT department,
           gender,
           COUNT(*) AS total
    FROM employees
    GROUP BY department, gender;

SQL creates groups based on the combination:

    department + gender

Result:

| department | gender | total |
|---|---|---:|
| IT | Male | 2 |
| IT | Female | 1 |
| HR | Male | 1 |
| HR | Female | 1 |

So:

    GROUP BY department

means:

    Create groups based on department.

While:

    GROUP BY department, gender

means:

    Create groups based on each unique department + gender combination.

---

# 16. GROUP BY Without Aggregate Function

You can use `GROUP BY` without an aggregate function.

    SELECT department
    FROM employees
    GROUP BY department;

This returns one result for each department.

However, if your goal is simply to get unique values, `DISTINCT` is usually clearer:

    SELECT DISTINCT department
    FROM employees;

The main reason we use `GROUP BY` is to perform calculations on groups.

---

# 17. WHERE vs GROUP BY

This is one of the most important differences to understand.

## WHERE

`WHERE` filters individual rows.

Example:

    SELECT *
    FROM employees
    WHERE salary > 50000;

Meaning:

    Which individual employees have salary greater than 50000?

So:

    WHERE
    ↓
    Filters rows

---

## GROUP BY

`GROUP BY` creates groups.

Example:

    SELECT department,
           AVG(salary)
    FROM employees
    GROUP BY department;

Meaning:

    Calculate the average salary separately for every department.

So:

    GROUP BY
    ↓
    Creates groups

---

# 18. HAVING

`HAVING` is used to filter groups.

The most important rule is:

    WHERE  → filters rows
    HAVING → filters groups

---

# 19. Why Do We Need HAVING?

Suppose we want:

> Departments having more than 2 employees.

First we create the groups:

    SELECT department,
           COUNT(*) AS total_employees
    FROM employees
    GROUP BY department;

Suppose the result is:

| department | total_employees |
|---|---:|
| IT | 5 |
| HR | 2 |
| Sales | 4 |
| Finance | 1 |

Now we only want departments having more than 2 employees.

We use:

    SELECT department,
           COUNT(*) AS total_employees
    FROM employees
    GROUP BY department
    HAVING COUNT(*) > 2;

Result:

| department | total_employees |
|---|---:|
| IT | 5 |
| Sales | 4 |

`HAVING` filtered the groups.

---

# 20. WHERE vs HAVING

This is extremely important.

Suppose we want:

> Employees whose salary is greater than 50000.

Use:

    WHERE salary > 50000

because salary belongs to an individual row.

But suppose we want:

> Departments whose average salary is greater than 50000.

Use:

    HAVING AVG(salary) > 50000

because `AVG(salary)` is calculated for a group.

Correct query:

    SELECT department,
           AVG(salary) AS average_salary
    FROM employees
    GROUP BY department
    HAVING AVG(salary) > 50000;

Remember:

    WHERE
    ↓
    Filters rows

    HAVING
    ↓
    Filters groups

---

# 21. HAVING with COUNT()

Example:

    SELECT department,
           COUNT(*) AS total_employees
    FROM employees
    GROUP BY department
    HAVING COUNT(*) >= 3;

Meaning:

    Group employees by department
    and return only departments
    having at least 3 employees.

---

# 22. HAVING with SUM()

Example:

    SELECT department,
           SUM(salary) AS total_salary
    FROM employees
    GROUP BY department
    HAVING SUM(salary) > 100000;

Meaning:

    Show only departments whose
    total salary is greater than 100000.

---

# 23. HAVING with AVG()

Example:

    SELECT department,
           AVG(salary) AS average_salary
    FROM employees
    GROUP BY department
    HAVING AVG(salary) > 50000;

Meaning:

    Show only departments whose
    average salary is greater than 50000.

---

# 24. HAVING with MIN()

Example:

    SELECT department,
           MIN(salary) AS minimum_salary
    FROM employees
    GROUP BY department
    HAVING MIN(salary) > 40000;

Meaning:

    Show departments where the
    minimum salary is greater than 40000.

---

# 25. HAVING with MAX()

Example:

    SELECT department,
           MAX(salary) AS maximum_salary
    FROM employees
    GROUP BY department
    HAVING MAX(salary) > 60000;

Meaning:

    Show departments where the
    maximum salary is greater than 60000.

---

# 26. WHERE + GROUP BY + HAVING

These three clauses can be used together.

Suppose we want:

> Consider only employees whose salary is above 40000, group them by department, and show only departments having at least 2 employees.

Query:

    SELECT department,
           COUNT(*) AS total_employees
    FROM employees
    WHERE salary > 40000
    GROUP BY department
    HAVING COUNT(*) >= 2;

Conceptually:

    All employees
          ↓
    WHERE salary > 40000
          ↓
    Filtered employees
          ↓
    GROUP BY department
          ↓
    Department groups
          ↓
    COUNT employees
          ↓
    HAVING COUNT(*) >= 2
          ↓
    Final result

This is a very important SQL pattern.

---

# 27. ORDER BY with GROUP BY

You can sort grouped results.

Suppose we want:

> Show the number of employees in each department and sort from highest to lowest.

    SELECT department,
           COUNT(*) AS total_employees
    FROM employees
    GROUP BY department
    ORDER BY total_employees DESC;

Result could be:

| department | total_employees |
|---|---:|
| IT | 5 |
| Sales | 4 |
| HR | 2 |
| Finance | 1 |

Here:

    GROUP BY
    ↓
    Creates department groups

    COUNT()
    ↓
    Calculates number of employees

    ORDER BY
    ↓
    Sorts the grouped result

---

# 28. GROUP BY + HAVING + ORDER BY

You can combine all three.

Example:

> Show departments having at least 2 employees and sort them by employee count from highest to lowest.

    SELECT department,
           COUNT(*) AS total_employees
    FROM employees
    GROUP BY department
    HAVING COUNT(*) >= 2
    ORDER BY total_employees DESC;

Conceptually:

    GROUP BY
    ↓
    Creates groups

    HAVING
    ↓
    Filters groups

    ORDER BY
    ↓
    Sorts the result

---

# 29. WHERE + GROUP BY + HAVING + ORDER BY

All four clauses can be combined.

Example:

> Consider employees whose salary is above 30000, group them by department, keep departments having at least 2 employees, and sort them by employee count from highest to lowest.

    SELECT department,
           COUNT(*) AS total_employees
    FROM employees
    WHERE salary > 30000
    GROUP BY department
    HAVING COUNT(*) >= 2
    ORDER BY total_employees DESC;

The logical flow is:

    All rows
       ↓
    WHERE
       ↓
    Filtered rows
       ↓
    GROUP BY
       ↓
    Groups
       ↓
    HAVING
       ↓
    Filtered groups
       ↓
    ORDER BY
       ↓
    Sorted result

---

# 30. Query Execution Order

The query is normally written like this:

    SELECT
    FROM
    WHERE
    GROUP BY
    HAVING
    ORDER BY

But conceptually, SQL processes these clauses approximately in this order:

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

This is called the **logical query processing order**.

---

# 31. Understanding the Execution Order

Consider:

    SELECT department,
           AVG(salary) AS average_salary
    FROM employees
    WHERE salary > 40000
    GROUP BY department
    HAVING AVG(salary) > 50000
    ORDER BY average_salary DESC;

Let's understand each step.

## Step 1 — FROM

SQL gets the data from:

    employees

---

## Step 2 — WHERE

SQL filters individual rows:

    salary > 40000

Only employees whose salary is greater than 40000 remain.

---

## Step 3 — GROUP BY

The remaining rows are grouped by:

    department

---

## Step 4 — HAVING

SQL calculates the group averages and keeps only groups where:

    AVG(salary) > 50000

---

## Step 5 — SELECT

SQL produces the requested columns:

    department
    average_salary

---

## Step 6 — ORDER BY

The final result is sorted:

    average_salary DESC

---

# 32. Written Order vs Execution Order

## Written SQL order

    SELECT
    FROM
    WHERE
    GROUP BY
    HAVING
    ORDER BY

## Logical execution order

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

This difference is very important.

You write `SELECT` first, but conceptually `FROM` is processed first.

---

# 33. Why WHERE Cannot Normally Replace HAVING

Suppose you want:

> Departments whose average salary is greater than 50000.

You might incorrectly write:

    SELECT department,
           AVG(salary)
    FROM employees
    WHERE AVG(salary) > 50000
    GROUP BY department;

This is generally invalid.

Why?

Because `WHERE` operates before grouping and aggregation.

At the `WHERE` stage, the department's average salary has not been calculated yet.

Therefore:

    WHERE
    ↓
    Row-level filtering

while:

    HAVING
    ↓
    Group-level filtering

Correct query:

    SELECT department,
           AVG(salary) AS average_salary
    FROM employees
    GROUP BY department
    HAVING AVG(salary) > 50000;

---

# 34. WHERE and HAVING Together

You can use both `WHERE` and `HAVING` in the same query.

Example:

> Consider only employees whose salary is above 30000. Then group them by department and show departments whose average salary is above 50000.

    SELECT department,
           AVG(salary) AS average_salary
    FROM employees
    WHERE salary > 30000
    GROUP BY department
    HAVING AVG(salary) > 50000;

The difference is:

    WHERE salary > 30000

filters individual employees.

Then:

    GROUP BY department

creates department groups.

Then:

    HAVING AVG(salary) > 50000

filters the resulting department groups.

---

# 35. ORDER BY vs WHERE vs GROUP BY vs HAVING

| Clause | Main Purpose | Works On |
|---|---|---|
| `WHERE` | Filters data | Individual rows |
| `GROUP BY` | Creates groups | Rows → Groups |
| `HAVING` | Filters groups | Groups |
| `ORDER BY` | Sorts result | Final result |

Easy way to remember:

    WHERE
    → Which rows do I want?

    GROUP BY
    → How should I divide the rows into groups?

    HAVING
    → Which groups do I want?

    ORDER BY
    → How should I sort the final result?

---

# 36. Real Data Analysis Example

Imagine we have a `sales` table:

| order_id | customer | category | amount |
|---:|---|---|---:|
| 1 | Ali | Laptop | 80000 |
| 2 | Ahmed | Mobile | 50000 |
| 3 | Salman | Laptop | 90000 |
| 4 | Ali | Mobile | 40000 |
| 5 | Hamza | Laptop | 70000 |

Suppose we want:

> Total sales for each category.

Query:

    SELECT category,
           SUM(amount) AS total_sales
    FROM sales
    GROUP BY category;

The result will be:

| category | total_sales |
|---|---:|
| Laptop | 240000 |
| Mobile | 90000 |

---

# 37. GROUP BY + HAVING in the Sales Example

Now suppose we want:

> Only categories whose total sales are greater than 100000.

Query:

    SELECT category,
           SUM(amount) AS total_sales
    FROM sales
    GROUP BY category
    HAVING SUM(amount) > 100000;

Result:

| category | total_sales |
|---|---:|
| Laptop | 240000 |

---

# 38. GROUP BY + HAVING + ORDER BY in the Sales Example

Now suppose we want:

> Show categories whose total sales are greater than 50000 and sort them from highest to lowest.

    SELECT category,
           SUM(amount) AS total_sales
    FROM sales
    GROUP BY category
    HAVING SUM(amount) > 50000
    ORDER BY total_sales DESC;

Result:

| category | total_sales |
|---|---:|
| Laptop | 240000 |
| Mobile | 90000 |

---

# 39. ORDER BY with Aggregate Functions

You can sort using an aggregate function.

Example:

    SELECT department,
           AVG(salary) AS average_salary
    FROM employees
    GROUP BY department
    ORDER BY AVG(salary) DESC;

You can also use the alias:

    SELECT department,
           AVG(salary) AS average_salary
    FROM employees
    GROUP BY department
    ORDER BY average_salary DESC;

The second version is usually easier to read.

---

# 40. ORDER BY Multiple Columns After GROUP BY

You can sort using multiple columns.

    SELECT department,
           COUNT(*) AS total_employees,
           AVG(salary) AS average_salary
    FROM employees
    GROUP BY department
    ORDER BY total_employees DESC,
             average_salary DESC;

The priority is:

    1. total_employees DESC
    2. average_salary DESC

SQL first sorts by employee count.

If two departments have the same employee count, then average salary determines their order.

---

# 41. GROUP BY and SELECT Rule

A very important rule:

When using `GROUP BY`, columns in the `SELECT` list generally need to be either:

1. Included in the `GROUP BY`, or
2. Used inside an aggregate function.

Valid:

    SELECT department,
           AVG(salary)
    FROM employees
    GROUP BY department;

Why is it valid?

Because:

    department
    ↓
    appears in GROUP BY

and:

    AVG(salary)
    ↓
    is an aggregate function

---

# 42. Invalid GROUP BY Example

This query is problematic:

    SELECT department,
           name,
           AVG(salary)
    FROM employees
    GROUP BY department;

Why?

Suppose the IT department contains:

    IT
    ├── Salman
    └── Ali

SQL has calculated one group for IT.

But which `name` should it display?

    Salman?

or:

    Ali?

There is no single answer.

Therefore, the query is not logically valid in standard SQL.

If you want to group by both department and name, you can write:

    SELECT department,
           name,
           AVG(salary)
    FROM employees
    GROUP BY department, name;

Now every unique department + name combination becomes a group.

---

# 43. GROUP BY Mental Model

Suppose the data is:

| department | salary |
|---|---:|
| IT | 50000 |
| IT | 60000 |
| HR | 45000 |
| HR | 55000 |
| Sales | 70000 |

When you write:

    GROUP BY department

Think of SQL conceptually separating the rows:

    IT
    ├── 50000
    └── 60000

    HR
    ├── 45000
    └── 55000

    Sales
    └── 70000

Then an aggregate function works separately inside each group.

For example:

    AVG(salary)

Conceptually becomes:

    IT
    → AVG(50000, 60000)
    → 55000

    HR
    → AVG(45000, 55000)
    → 50000

    Sales
    → AVG(70000)
    → 70000

This is one of the easiest ways to understand `GROUP BY`.

---

# 44. The Most Important Mental Model

Remember this complete flow:

    Individual Rows
          ↓
        WHERE
          ↓
    Filtered Rows
          ↓
       GROUP BY
          ↓
        Groups
          ↓
    Aggregate Functions
          ↓
     Group Results
          ↓
        HAVING
          ↓
    Filtered Groups
          ↓
      ORDER BY
          ↓
    Sorted Final Result

This mental model will help you understand more advanced SQL queries later.

---

# 45. Common Mistake 1 — Using WHERE for Aggregate Filtering

Wrong:

    SELECT department,
           COUNT(*)
    FROM employees
    GROUP BY department
    WHERE COUNT(*) > 2;

Correct:

    SELECT department,
           COUNT(*)
    FROM employees
    GROUP BY department
    HAVING COUNT(*) > 2;

Why?

Because `COUNT(*)` is a group-level calculation.

Therefore, use:

    HAVING COUNT(*) > 2

---

# 46. Common Mistake 2 — Forgetting GROUP BY

Suppose you write:

    SELECT department,
           COUNT(*)
    FROM employees;

You are asking SQL for:

    department
    +
    one overall count

But you have not told SQL to create separate department groups.

Usually, you need:

    SELECT department,
           COUNT(*)
    FROM employees
    GROUP BY department;

---

# 47. Common Mistake 3 — Thinking WHERE and HAVING Are the Same

They are not.

    WHERE
    → Filters individual rows

    HAVING
    → Filters groups

Example:

    WHERE salary > 50000

means:

    Find individual employees whose salary is above 50000.

While:

    HAVING AVG(salary) > 50000

means:

    Find groups whose average salary is above 50000.

---

# 48. Common Mistake 4 — Thinking GROUP BY Sorts Data

`GROUP BY` is used for grouping, not sorting.

If you want sorting, use:

    ORDER BY

For example:

    SELECT department,
           COUNT(*)
    FROM employees
    GROUP BY department
    ORDER BY department;

Here:

    GROUP BY
    ↓
    Creates groups

and:

    ORDER BY
    ↓
    Sorts the result

---

# 49. Common Mistake 5 — Thinking ORDER BY Creates Groups

`ORDER BY` does not create groups.

For example:

    ORDER BY department

only sorts the rows based on department.

It does not combine rows.

To create groups, use:

    GROUP BY department

---

# 50. ORDER BY vs GROUP BY

These two clauses may look similar, but their purposes are completely different.

## ORDER BY

Purpose:

    Sort rows.

Example:

    SELECT *
    FROM employees
    ORDER BY salary DESC;

Meaning:

    Show employees from highest salary to lowest salary.

## GROUP BY

Purpose:

    Create groups.

Example:

    SELECT department,
           AVG(salary)
    FROM employees
    GROUP BY department;

Meaning:

    Calculate average salary separately for each department.

Remember:

    ORDER BY
    → Sorting

    GROUP BY
    → Grouping

---

# 51. GROUP BY vs DISTINCT

Both can sometimes produce unique values, but their main purposes are different.

`DISTINCT`:

    SELECT DISTINCT department
    FROM employees;

Purpose:

    Remove duplicate department values.

`GROUP BY`:

    SELECT department,
           COUNT(*)
    FROM employees
    GROUP BY department;

Purpose:

    Create groups so that calculations can be performed for each group.

Simple rule:

    DISTINCT
    → Give me unique values.

    GROUP BY
    → Give me groups so I can perform calculations on them.

---

# 52. Final Summary of ORDER BY

`ORDER BY` is used to sort results.

Examples:

    ORDER BY salary ASC;

    ORDER BY salary DESC;

    ORDER BY department ASC, salary DESC;

Remember:

    ASC
    → Small to Large
    → A to Z

    DESC
    → Large to Small
    → Z to A

---

# 53. Final Summary of GROUP BY

`GROUP BY` creates groups based on one or more columns.

Example:

    SELECT department,
           COUNT(*)
    FROM employees
    GROUP BY department;

Remember:

    GROUP BY
    → Rows → Groups

It is commonly used with:

    COUNT()
    SUM()
    AVG()
    MIN()
    MAX()

---

# 54. Final Summary of HAVING

`HAVING` filters groups.

Example:

    SELECT department,
           COUNT(*)
    FROM employees
    GROUP BY department
    HAVING COUNT(*) > 2;

Remember:

    HAVING
    → Filters Groups

---

# 55. Final Summary of WHERE

`WHERE` filters individual rows.

Example:

    SELECT *
    FROM employees
    WHERE salary > 50000;

Remember:

    WHERE
    → Filters Rows

---

# 56. WHERE vs GROUP BY vs HAVING vs ORDER BY

The easiest way to remember everything:

    WHERE
    ↓
    Filter individual rows

    GROUP BY
    ↓
    Create groups

    HAVING
    ↓
    Filter groups

    ORDER BY
    ↓
    Sort the final result

Another way to remember:

    WHERE
    → Which rows do I want?

    GROUP BY
    → How should I divide those rows into groups?

    HAVING
    → Which groups do I want?

    ORDER BY
    → How should I sort the final result?

---

# 57. Query Execution Order — Final Version

The SQL query is written as:

    SELECT
    FROM
    WHERE
    GROUP BY
    HAVING
    ORDER BY

But conceptually SQL processes it approximately as:

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

Therefore:

    FROM
    → Get the data

    WHERE
    → Filter individual rows

    GROUP BY
    → Create groups

    HAVING
    → Filter groups

    SELECT
    → Decide what columns/expressions to return

    ORDER BY
    → Sort the final result

---

# 58. Complete Example

Consider this query:

    SELECT department,
           COUNT(*) AS total_employees,
           AVG(salary) AS average_salary
    FROM employees
    WHERE salary > 30000
    GROUP BY department
    HAVING COUNT(*) >= 2
    ORDER BY average_salary DESC;

Understand it step by step:

### FROM

Get data from:

    employees

### WHERE

Keep employees whose salary is:

    salary > 30000

### GROUP BY

Create groups based on:

    department

### Aggregate Functions

For every department calculate:

    COUNT(*)

and:

    AVG(salary)

### HAVING

Keep only departments where:

    COUNT(*) >= 2

### SELECT

Return:

    department
    total_employees
    average_salary

### ORDER BY

Sort the final result using:

    average_salary DESC

Complete logical flow:

    employees
        ↓
    WHERE salary > 30000
        ↓
    GROUP BY department
        ↓
    COUNT() and AVG()
        ↓
    HAVING COUNT(*) >= 2
        ↓
    SELECT required columns
        ↓
    ORDER BY average_salary DESC
        ↓
    Final Result

---

# 59. Current SQL Learning Progress

So far, the learning sequence is:

    01. Database Fundamentals
          ↓
    02. DDL
          ↓
    03. DML
          ↓
    04. SELECT
          ↓
    05. WHERE
          ↓
    06. Comparison Operators
          ↓
    07. Logical Operators
          ↓
    08. Scalar Operations
          ↓
    09. Aggregate Operations
          ↓
    10. Query Execution Order
          ↓
    11. ORDER BY
          ↓
    12. GROUP BY
          ↓
    13. HAVING

---

# 60. Important Concepts Learned in This Topic

By completing this topic, you should understand:

- What `ORDER BY` does.
- Difference between `ASC` and `DESC`.
- Sorting using one column.
- Sorting using multiple columns.
- Sorting text and numeric columns.
- Sorting calculated values.
- What `GROUP BY` does.
- Why `GROUP BY` is used with aggregate functions.
- `GROUP BY` with `COUNT()`.
- `GROUP BY` with `SUM()`.
- `GROUP BY` with `AVG()`.
- `GROUP BY` with `MIN()`.
- `GROUP BY` with `MAX()`.
- Grouping using multiple columns.
- Difference between `WHERE` and `GROUP BY`.
- What `HAVING` does.
- Difference between `WHERE` and `HAVING`.
- `HAVING` with `COUNT()`.
- `HAVING` with `SUM()`.
- `HAVING` with `AVG()`.
- `HAVING` with `MIN()`.
- `HAVING` with `MAX()`.
- Combining `WHERE`, `GROUP BY`, and `HAVING`.
- Combining `GROUP BY`, `HAVING`, and `ORDER BY`.
- Combining `WHERE`, `GROUP BY`, `HAVING`, and `ORDER BY`.
- Logical query execution order.
- Difference between written query order and execution order.
- Rules for columns used with `GROUP BY`.
- Common mistakes with `WHERE`, `GROUP BY`, `HAVING`, and `ORDER BY`.

---

# 61. One-Line Revision

Remember these four statements:

    WHERE
    → Filters rows.

    GROUP BY
    → Creates groups.

    HAVING
    → Filters groups.

    ORDER BY
    → Sorts the result.

And remember the logical execution order:

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

These concepts are the foundation for writing more advanced SQL queries.