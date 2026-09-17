# SQL JOINs and Set Operations

## 1. What is a SQL JOIN?

A **JOIN** is used to combine rows from two or more tables based on a related column.

For example, suppose we have two tables:

### Customers

| customer_id | customer_name |
|---:|---|
| 1 | Salman |
| 2 | Ali |
| 3 | Ahmed |
| 4 | Hamza |

### Orders

| order_id | customer_id | amount |
|---:|---:|---:|
| 101 | 1 | 5000 |
| 102 | 2 | 3000 |
| 103 | 1 | 7000 |
| 104 | 5 | 2000 |

Both tables have a related column:

`customer_id`

We can use this column to connect the tables.

Example:

    SELECT customers.customer_name,
           orders.order_id,
           orders.amount
    FROM customers
    INNER JOIN orders
    ON customers.customer_id = orders.customer_id;

The important part is:

    ON customers.customer_id = orders.customer_id

The `ON` clause defines how the two tables should be related.

---

# 2. Why Do We Need JOINs?

In a relational database, information is normally divided into different tables instead of putting everything into one huge table.

For example, instead of storing:

| customer_id | customer_name | phone | order_id | amount |
|---:|---|---|---:|---:|
| 1 | Salman | 0300... | 101 | 5000 |
| 1 | Salman | 0300... | 103 | 7000 |
| 2 | Ali | 0311... | 102 | 3000 |

we can separate the information.

### Customers

- customer_id
- customer_name
- phone

### Orders

- order_id
- customer_id
- amount

Then JOIN allows us to bring related information together when we need it.

Simple idea:

    Table A
       +
    Table B
       ↓
      JOIN
       ↓
    Combined Result

---

# 3. Primary Key and Foreign Key Relationship

JOINs become much easier to understand when you understand **Primary Keys** and **Foreign Keys**.

Suppose we have:

### Customers

| customer_id | customer_name |
|---:|---|
| 1 | Salman |
| 2 | Ali |
| 3 | Ahmed |

Here:

`customer_id`

is the **Primary Key**.

It uniquely identifies each customer.

Now suppose we have:

### Orders

| order_id | customer_id | amount |
|---:|---:|---:|
| 101 | 1 | 5000 |
| 102 | 2 | 3000 |
| 103 | 1 | 7000 |

Here:

`customer_id`

is a **Foreign Key** referring to the customer's `customer_id`.

Relationship:

    customers.customer_id
             ↑
             |
             |
    orders.customer_id

This relationship is commonly used for JOINs.

---

# 4. Types of SQL JOINs

The major JOIN types are:

- INNER JOIN
- LEFT JOIN
- RIGHT JOIN
- FULL OUTER JOIN
- SELF JOIN

The most important ones to master first are:

- INNER JOIN
- LEFT JOIN
- RIGHT JOIN

---

# 5. INNER JOIN

`INNER JOIN` returns only the rows where a matching record exists in both tables.

Example:

    SELECT customers.customer_name,
           orders.order_id,
           orders.amount
    FROM customers
    INNER JOIN orders
    ON customers.customer_id = orders.customer_id;

Suppose the result is:

| customer_name | order_id | amount |
|---|---:|---:|
| Salman | 101 | 5000 |
| Ali | 102 | 3000 |
| Salman | 103 | 7000 |

Why didn't Ahmed and Hamza appear?

Because they don't have matching orders.

Why didn't order `104` appear?

Because `customer_id = 5` doesn't exist in the customers table.

Therefore:

    INNER JOIN
    → Only matching rows from both tables

---

# 6. LEFT JOIN

`LEFT JOIN` returns:

> All rows from the left table + matching rows from the right table.

If there is no match in the right table, SQL returns `NULL`.

Example:

    SELECT customers.customer_name,
           orders.order_id,
           orders.amount
    FROM customers
    LEFT JOIN orders
    ON customers.customer_id = orders.customer_id;

Result:

| customer_name | order_id | amount |
|---|---:|---:|
| Salman | 101 | 5000 |
| Salman | 103 | 7000 |
| Ali | 102 | 3000 |
| Ahmed | NULL | NULL |
| Hamza | NULL | NULL |

Ahmed and Hamza are still present because they are in the left table.

They don't have matching orders, so the columns coming from the right table become `NULL`.

Remember:

    LEFT JOIN
    → Keep everything from LEFT table
    → Add matching data from RIGHT table
    → No match = NULL

---

# 7. RIGHT JOIN

`RIGHT JOIN` is essentially the opposite direction of `LEFT JOIN`.

It returns:

> All rows from the right table + matching rows from the left table.

Example:

    SELECT customers.customer_name,
           orders.order_id,
           orders.amount
    FROM customers
    RIGHT JOIN orders
    ON customers.customer_id = orders.customer_id;

Result:

| customer_name | order_id | amount |
|---|---:|---:|
| Salman | 101 | 5000 |
| Ali | 102 | 3000 |
| Salman | 103 | 7000 |
| NULL | 104 | 2000 |

Order `104` remains because it exists in the right table.

But there is no matching customer.

Therefore:

`customer_name = NULL`

Remember:

    RIGHT JOIN
    → Keep everything from RIGHT table
    → Add matching data from LEFT table
    → No match = NULL

---

# 8. LEFT JOIN vs RIGHT JOIN

Suppose:

    A LEFT JOIN B

means:

    Keep ALL A
    +
    Matching B

While:

    A RIGHT JOIN B

means:

    Keep ALL B
    +
    Matching A

Easy memory:

    LEFT JOIN
    → Protect the left table.

    RIGHT JOIN
    → Protect the right table.

In practice, many developers prefer `LEFT JOIN` because you can simply swap the table order instead of using `RIGHT JOIN`.

For example:

    A RIGHT JOIN B

can generally be rewritten as:

    B LEFT JOIN A

---

# 9. FULL OUTER JOIN

`FULL OUTER JOIN` returns:

> All matching and non-matching rows from both tables.

Conceptually:

    Everything from LEFT
    +
    Everything from RIGHT

Example:

    SELECT customers.customer_name,
           orders.order_id,
           orders.amount
    FROM customers
    FULL OUTER JOIN orders
    ON customers.customer_id = orders.customer_id;

Conceptually, the result contains:

| customer_name | order_id | amount |
|---|---:|---:|
| Salman | 101 | 5000 |
| Salman | 103 | 7000 |
| Ali | 102 | 3000 |
| Ahmed | NULL | NULL |
| Hamza | NULL | NULL |
| NULL | 104 | 2000 |

So:

    Customers without orders
            ↓
         included

    Orders without customers
            ↓
         included

    Matching records
            ↓
         included

## Important MySQL Note

MySQL does not provide `FULL OUTER JOIN` as a direct JOIN keyword.

In MySQL, it can be simulated using combinations such as:

    LEFT JOIN
    +
    RIGHT JOIN
    +
    UNION

---

# 10. SELF JOIN

A `SELF JOIN` means joining a table with itself.

Why would we need this?

Suppose we have an employee table:

| employee_id | employee_name | manager_id |
|---:|---|---:|
| 1 | Salman | NULL |
| 2 | Ali | 1 |
| 3 | Ahmed | 1 |
| 4 | Hamza | 2 |

Here:

`employee_id`

identifies the employee.

And:

`manager_id`

contains another employee's ID.

For example:

    Ali → manager is Salman
    Ahmed → manager is Salman
    Hamza → manager is Ali

The employee and manager are both stored in the same table.

We can use a SELF JOIN:

    SELECT employee.employee_name AS employee,
           manager.employee_name AS manager
    FROM employees AS employee
    LEFT JOIN employees AS manager
    ON employee.manager_id = manager.employee_id;

Result:

| employee | manager |
|---|---|
| Salman | NULL |
| Ali | Salman |
| Ahmed | Salman |
| Hamza | Ali |

Notice that we used the same table twice:

    employees AS employee
    employees AS manager

The aliases make SQL treat the same table as two logical copies.

---

# 11. Why Are Aliases Important in SELF JOIN?

Without aliases, SQL would not clearly know which instance of the table you're referring to.

So:

    employees AS employee

means:

> Treat this copy as the employee.

And:

    employees AS manager

means:

> Treat this copy as the manager.

Then:

    employee.manager_id = manager.employee_id

connects them.

---

# 12. JOIN Using Table Aliases

Aliases are useful not only for SELF JOINs.

Instead of writing:

    SELECT customers.customer_name,
           orders.order_id
    FROM customers
    INNER JOIN orders
    ON customers.customer_id = orders.customer_id;

you can write:

    SELECT c.customer_name,
           o.order_id
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id;

Here:

    c → customers
    o → orders

Aliases make JOIN queries shorter and easier to read.

---

# 13. JOIN with WHERE

JOINs can be combined with `WHERE`.

Example:

> Show orders only for customers whose name is Salman.

    SELECT c.customer_name,
           o.order_id,
           o.amount
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    WHERE c.customer_name = 'Salman';

Here:

    ON
    → Defines the relationship between tables.

    WHERE
    → Filters the rows.

---

# 14. JOIN with ORDER BY

You can sort joined results.

Example:

    SELECT c.customer_name,
           o.order_id,
           o.amount
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    ORDER BY o.amount DESC;

This sorts the combined result by order amount from highest to lowest.

---

# 15. JOIN with GROUP BY

You can also group joined data.

Suppose we want:

> Total amount spent by each customer.

    SELECT c.customer_name,
           SUM(o.amount) AS total_spent
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.customer_name;

Result:

| customer_name | total_spent |
|---|---:|
| Salman | 12000 |
| Ali | 3000 |

Here:

    JOIN
    → Connect customers and orders

    SUM()
    → Calculate total spending

    GROUP BY
    → Create one group for each customer

---

# 16. JOIN with HAVING

Suppose we want:

> Customers whose total spending is greater than 5000.

    SELECT c.customer_name,
           SUM(o.amount) AS total_spent
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.customer_name
    HAVING SUM(o.amount) > 5000;

Now:

    JOIN
    → Connect customers and orders

    GROUP BY
    → Create one group per customer

    SUM()
    → Calculate total spending

    HAVING
    → Keep customers whose total is greater than 5000

---

# 17. Joining More Than Two Tables

You are not limited to joining only two tables.

You can join:

    2 tables
    3 tables
    4 tables
    5 tables
    ...

as long as the tables have relationships that allow you to connect them.

---

# 18. Example with Three Tables

Suppose we have:

### Customers

| customer_id | customer_name |
|---:|---|
| 1 | Salman |
| 2 | Ali |
| 3 | Ahmed |

### Orders

| order_id | customer_id | product_id |
|---:|---:|---:|
| 101 | 1 | 10 |
| 102 | 2 | 11 |
| 103 | 1 | 12 |

### Products

| product_id | product_name | price |
|---:|---|---:|
| 10 | Laptop | 80000 |
| 11 | Mouse | 3000 |
| 12 | Keyboard | 5000 |

Relationships:

    Customers
        |
        | customer_id
        ↓
    Orders
        |
        | product_id
        ↓
    Products

We can combine all three:

    SELECT c.customer_name,
           o.order_id,
           p.product_name,
           p.price
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    INNER JOIN products AS p
    ON o.product_id = p.product_id;

Result:

| customer_name | order_id | product_name | price |
|---|---:|---|---:|
| Salman | 101 | Laptop | 80000 |
| Ali | 102 | Mouse | 3000 |
| Salman | 103 | Keyboard | 5000 |

---

# 19. How Three-Table JOIN Works

Think of it step by step.

First:

    Customers
        +
    Orders

using:

    customer_id

Then the result is joined with:

    Products

using:

    product_id

So:

    Customers
        ↓
    customer_id
        ↓
    Orders
        ↓
    product_id
        ↓
    Products

You don't need to join every table directly to every other table.

You follow the relationships between them.

---

# 20. Four Tables

Suppose we have:

    customers
    orders
    products
    categories

Relationships:

    customers
        ↓
    orders
        ↓
    products
        ↓
    categories

Query:

    SELECT c.customer_name,
           o.order_id,
           p.product_name,
           cat.category_name
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    INNER JOIN products AS p
    ON o.product_id = p.product_id
    INNER JOIN categories AS cat
    ON p.category_id = cat.category_id;

Now four tables are combined.

---

# 21. Important Rule for Multiple JOINs

When joining multiple tables, always ask:

> What column connects this table to the previous table?

For example:

    customers.customer_id
            ↓
    orders.customer_id

Then:

    orders.product_id
            ↓
    products.product_id

Then:

    products.category_id
            ↓
    categories.category_id

This creates a chain:

    Customers
        ↓
    Orders
        ↓
    Products
        ↓
    Categories

---

# 22. JOIN Conditions

The `ON` clause tells SQL **how two tables are related**.

Example:

    ON c.customer_id = o.customer_id

This means:

> Match a customer with an order when their customer IDs are equal.

Another example:

    ON o.product_id = p.product_id

means:

> Match an order with a product when their product IDs are equal.

So:

    JOIN
    → Which tables should I combine?

    ON
    → How should I match their rows?

---

# 23. JOIN vs WHERE

This is an important distinction.

Consider:

    SELECT *
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    WHERE o.amount > 5000;

Here:

    ON
    → Defines the relationship between tables.

    WHERE
    → Filters the resulting rows.

Do not confuse these two.

---

# 24. INNER JOIN with Multiple Tables

Example:

    SELECT c.customer_name,
           o.order_id,
           p.product_name
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    INNER JOIN products AS p
    ON o.product_id = p.product_id;

This returns only records where all required relationships have matching rows.

If an order has no matching product, an `INNER JOIN` to `products` will remove that order from the result.

---

# 25. LEFT JOIN with Multiple Tables

You can also use multiple `LEFT JOIN`s.

    SELECT c.customer_name,
           o.order_id,
           p.product_name
    FROM customers AS c
    LEFT JOIN orders AS o
    ON c.customer_id = o.customer_id
    LEFT JOIN products AS p
    ON o.product_id = p.product_id;

The important idea is:

    All customers
        ↓
    Keep them
        ↓
    Add matching orders
        ↓
    Add matching products

Customers without orders can still appear.

---

# 26. Mixing Different JOIN Types

You can combine JOIN types.

Example:

    SELECT c.customer_name,
           o.order_id,
           p.product_name
    FROM customers AS c
    LEFT JOIN orders AS o
    ON c.customer_id = o.customer_id
    INNER JOIN products AS p
    ON o.product_id = p.product_id;

But be careful.

Changing `LEFT JOIN` to `INNER JOIN` can remove rows.

When writing complex queries, always think about which table's rows you want to preserve.

---

# 27. Set Operations

JOINs combine tables by matching related rows.

Set operations work differently.

Set operations combine query results.

For example:

    Query 1 result
    --------------
    A
    B
    C

    +

    Query 2 result
    --------------
    D
    E
    F

    ↓

    Combined result
    --------------
    A
    B
    C
    D
    E
    F

Important set operations include:

- UNION
- UNION ALL
- INTERSECT
- EXCEPT

---

# 28. UNION

`UNION` combines the results of two `SELECT` statements and removes duplicate rows.

Example:

    SELECT name
    FROM customers_2025

    UNION

    SELECT name
    FROM customers_2026;

Suppose the first query returns:

    Ali
    Salman
    Ahmed

and the second returns:

    Salman
    Hamza

The UNION result becomes:

    Ali
    Salman
    Ahmed
    Hamza

`Salman` appeared in both results, but only one copy remains.

Remember:

    UNION
    → Combine results
    → Remove duplicates

---

# 29. UNION ALL

`UNION ALL` also combines results, but it does not remove duplicates.

Example:

    SELECT name
    FROM customers_2025

    UNION ALL

    SELECT name
    FROM customers_2026;

Result:

    Ali
    Salman
    Ahmed
    Salman
    Hamza

`Salman` appears twice because it existed in both result sets.

Remember:

    UNION
    → Removes duplicates

    UNION ALL
    → Keeps duplicates

`UNION ALL` is generally faster because SQL does not need to perform duplicate elimination.

---

# 30. UNION vs UNION ALL

| Feature | UNION | UNION ALL |
|---|---|---|
| Combines results | Yes | Yes |
| Removes duplicates | Yes | No |
| Keeps duplicates | No | Yes |
| Usually faster | No | Yes |

Easy memory:

    UNION
    → Unique result

    UNION ALL
    → Everything

---

# 31. Requirements for UNION

The queries used with `UNION` should have compatible structures.

For example:

    SELECT name, age
    FROM students

    UNION

    SELECT name, age
    FROM employees;

Both queries return:

    2 columns

The corresponding columns should have compatible data types.

A good rule is:

    Same number of columns
    +
    Compatible data types
    +
    Same logical order

---

# 32. INTERSECT

`INTERSECT` returns rows that exist in both query results.

Suppose Query 1 returns:

    Ali
    Salman
    Ahmed

Query 2 returns:

    Salman
    Ahmed
    Hamza

Then:

    SELECT name
    FROM table1

    INTERSECT

    SELECT name
    FROM table2;

Result:

    Salman
    Ahmed

Because these values exist in both results.

Remember:

    INTERSECT
    → Common rows

---

# 33. EXCEPT

`EXCEPT` returns rows from the first query that are not present in the second query.

Suppose:

Query 1:

    Ali
    Salman
    Ahmed

Query 2:

    Salman
    Ahmed
    Hamza

Then:

    SELECT name
    FROM table1

    EXCEPT

    SELECT name
    FROM table2;

Result:

    Ali

Because Ali exists in the first result but not in the second.

Remember:

    EXCEPT
    → First result minus second result

---

# 34. Set Operations Mental Model

Imagine:

    A = {1, 2, 3, 4}
    B = {3, 4, 5, 6}

### UNION

    A ∪ B

    {1, 2, 3, 4, 5, 6}

### INTERSECT

    A ∩ B

    {3, 4}

### EXCEPT

    A - B

    {1, 2}

This is the easiest way to understand set operations.

---

# 35. Important MySQL Note

If you are using MySQL, there is an important practical detail.

Modern MySQL supports:

    UNION
    UNION ALL
    INTERSECT
    EXCEPT

`INTERSECT` and `EXCEPT` are available in newer MySQL versions.

If you are using an older MySQL version, they may not be available directly.

In that case, similar results can often be produced using:

    JOIN
    NOT EXISTS
    Subqueries

For learning SQL, understand the set-operation concepts first.

---

# 36. JOIN vs SET OPERATIONS

This difference is very important.

## JOIN

JOIN combines related columns from different tables.

Example:

    Customers + Orders

Result:

    customer_name | order_id | amount

So JOIN generally expands the result horizontally.

## UNION

UNION combines rows from separate query results.

Example:

    Customers_2025
    +
    Customers_2026

Result:

    customer names from both years

So UNION generally combines results vertically.

Simple memory:

    JOIN
    → Side by side

    UNION
    → One after another

---

# 37. Example: JOIN vs UNION

Suppose:

### Table A

| id | name |
|---:|---|
| 1 | Salman |
| 2 | Ali |

### Table B

| id | salary |
|---:|---:|
| 1 | 50000 |
| 2 | 60000 |

JOIN:

    SELECT *
    FROM A
    JOIN B
    ON A.id = B.id;

Result:

| id | name | salary |
|---:|---|---:|
| 1 | Salman | 50000 |
| 2 | Ali | 60000 |

Columns were combined.

Now suppose:

### Employees_2025

| name |
|---|
| Salman |
| Ali |

### Employees_2026

| name |
|---|
| Ahmed |
| Hamza |

UNION:

    SELECT name
    FROM employees_2025

    UNION

    SELECT name
    FROM employees_2026;

Result:

| name |
|---|
| Salman |
| Ali |
| Ahmed |
| Hamza |

Rows were combined.

---

# 38. JOIN More Than Two Tables — Real Example

Suppose we have four tables:

    customers
    orders
    products
    categories

Relationships:

    customers
        ↓
    orders
        ↓
    products
        ↓
    categories

We want:

> Customer name, order ID, product name, category name, and product price.

Query:

    SELECT c.customer_name,
           o.order_id,
           p.product_name,
           cat.category_name,
           p.price
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    INNER JOIN products AS p
    ON o.product_id = p.product_id
    INNER JOIN categories AS cat
    ON p.category_id = cat.category_id;

Read this query like a chain:

    customers
        ↓
    customer_id
        ↓
    orders
        ↓
    product_id
        ↓
    products
        ↓
    category_id
        ↓
    categories

This is the key to understanding multi-table JOINs.

---

# 39. Multi-Table JOIN with Aggregation

Suppose `orders` contains `quantity`, and `products` contains `price`.

We want:

> Total sales for each customer.

We can calculate:

    quantity × price

Then sum it for each customer.

    SELECT c.customer_name,
           SUM(o.quantity * p.price) AS total_sales
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    INNER JOIN products AS p
    ON o.product_id = p.product_id
    GROUP BY c.customer_id, c.customer_name;

Here several concepts work together:

    JOIN
    ↓
    Connect tables

    quantity * price
    ↓
    Scalar calculation

    SUM()
    ↓
    Aggregate calculation

    GROUP BY
    ↓
    One group per customer

This is very close to the kind of SQL you will use in real data analysis.

---

# 40. Multi-Table JOIN with WHERE

Suppose we only want products named `Laptop`.

    SELECT c.customer_name,
           p.product_name,
           p.price
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    INNER JOIN products AS p
    ON o.product_id = p.product_id
    WHERE p.product_name = 'Laptop';

The JOIN establishes relationships.

The WHERE filters the joined rows.

---

# 41. Multi-Table JOIN with GROUP BY and HAVING

Suppose:

> Find customers whose total purchases are greater than 100000.

    SELECT c.customer_name,
           SUM(o.quantity * p.price) AS total_spent
    FROM customers AS c
    INNER JOIN orders AS o
    ON c.customer_id = o.customer_id
    INNER JOIN products AS p
    ON o.product_id = p.product_id
    GROUP BY c.customer_id, c.customer_name
    HAVING SUM(o.quantity * p.price) > 100000
    ORDER BY total_spent DESC;

Now you are combining:

    JOIN
    ↓
    GROUP BY
    ↓
    Aggregate Functions
    ↓
    HAVING
    ↓
    ORDER BY

This is a very important SQL milestone.

---

# 42. Complete SQL Mental Model So Far

For a complex query, a useful conceptual order is:

    FROM
    ↓
    JOIN
    ↓
    ON
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

Conceptually:

    FROM
    → Get the tables

    JOIN + ON
    → Connect related tables

    WHERE
    → Filter individual rows

    GROUP BY
    → Create groups

    HAVING
    → Filter groups

    SELECT
    → Choose the final output

    ORDER BY
    → Sort the final result

Note: This is a simplified learning model. SQL's actual logical query processing has more detail, but this sequence is useful for understanding how these clauses work together.

---

# 43. Quick JOIN Revision

## INNER JOIN

    Only matching rows.

Syntax:

    SELECT ...
    FROM table1
    INNER JOIN table2
    ON table1.id = table2.id;

## LEFT JOIN

    All rows from the left table
    +
    matching rows from the right table.

Syntax:

    SELECT ...
    FROM table1
    LEFT JOIN table2
    ON table1.id = table2.id;

## RIGHT JOIN

    All rows from the right table
    +
    matching rows from the left table.

Syntax:

    SELECT ...
    FROM table1
    RIGHT JOIN table2
    ON table1.id = table2.id;

## FULL OUTER JOIN

    All rows from both tables.

Syntax where supported:

    SELECT ...
    FROM table1
    FULL OUTER JOIN table2
    ON table1.id = table2.id;

## SELF JOIN

    A table joined with itself.

Example:

    SELECT e.employee_name,
           m.employee_name AS manager_name
    FROM employees AS e
    LEFT JOIN employees AS m
    ON e.manager_id = m.employee_id;

---

# 44. Quick Set Operation Revision

## UNION

Combines two result sets and removes duplicates.

    SELECT name FROM table1
    UNION
    SELECT name FROM table2;

## UNION ALL

Combines two result sets and keeps duplicates.

    SELECT name FROM table1
    UNION ALL
    SELECT name FROM table2;

## INTERSECT

Returns common rows.

    SELECT name FROM table1
    INTERSECT
    SELECT name FROM table2;

## EXCEPT

Returns rows from the first query that are not in the second query.

    SELECT name FROM table1
    EXCEPT
    SELECT name FROM table2;

---

# 45. The Most Important Difference

Remember this:

    JOIN
    → Combines columns from related tables.

    UNION
    → Combines rows from compatible query results.

Or even simpler:

    JOIN
    → Horizontal combination

    UNION
    → Vertical combination

---

# 46. What You Should Be Able to Do Now

After learning this topic, you should be able to:

- Explain why JOINs are needed.
- Understand relationships between tables.
- Understand primary key and foreign key relationships.
- Use `INNER JOIN`.
- Use `LEFT JOIN`.
- Use `RIGHT JOIN`.
- Understand `FULL OUTER JOIN`.
- Understand why MySQL differs regarding FULL OUTER JOIN.
- Use `SELF JOIN`.
- Understand table aliases.
- Join two tables.
- Join three tables.
- Join four or more tables.
- Understand the `ON` clause.
- Combine JOIN with `WHERE`.
- Combine JOIN with `GROUP BY`.
- Combine JOIN with `HAVING`.
- Combine JOIN with `ORDER BY`.
- Use multiple JOINs in a single query.
- Understand `UNION`.
- Understand `UNION ALL`.
- Understand `INTERSECT`.
- Understand `EXCEPT`.
- Understand the difference between JOIN and SET operations.
- Understand how relational tables are connected together.

---

# 47. Final JOIN Mental Model

The most important concepts to remember are:

    JOIN
    → Connect related tables.

    INNER JOIN
    → Matching records only.

    LEFT JOIN
    → Keep everything from the left table.

    RIGHT JOIN
    → Keep everything from the right table.

    FULL OUTER JOIN
    → Keep everything from both tables.

    SELF JOIN
    → Join a table with itself.

---

# 48. Final Set Operation Mental Model

    UNION
    → Combine result sets and remove duplicates.

    UNION ALL
    → Combine result sets and keep duplicates.

    INTERSECT
    → Find common rows.

    EXCEPT
    → Find rows in the first result but not the second.

---

# 49. Multi-Table JOIN Mental Model

The most important thing when joining multiple tables is to identify the relationship between them.

For example:

    Table A
       ↓
    A.primary_key = B.foreign_key
       ↓
    Table B
       ↓
    B.primary_key = C.foreign_key
       ↓
    Table C
       ↓
    C.primary_key = D.foreign_key
       ↓
    Table D

You don't need to join every table directly to every other table.

Follow the relationship chain.

Example:

    Customers
        ↓
    Orders
        ↓
    Products
        ↓
    Categories

And the SQL query follows the same chain:

    SELECT ...
    FROM customers
    JOIN orders
        ON ...
    JOIN products
        ON ...
    JOIN categories
        ON ...;

---

# 50. Final Cheat Sheet

| Concept | Meaning |
|---|---|
| `INNER JOIN` | Matching rows from both tables |
| `LEFT JOIN` | All left rows + matching right rows |
| `RIGHT JOIN` | All right rows + matching left rows |
| `FULL OUTER JOIN` | All rows from both tables |
| `SELF JOIN` | A table joined with itself |
| `ON` | Defines how tables are related |
| `UNION` | Combines results and removes duplicates |
| `UNION ALL` | Combines results and keeps duplicates |
| `INTERSECT` | Returns common rows |
| `EXCEPT` | First result minus second result |
| `JOIN` | Horizontal combination |
| `UNION` | Vertical combination |

---

# 51. Final Summary

The most important ideas from this topic are:

    JOIN
    → Connect related tables.

    INNER JOIN
    → Only matching records.

    LEFT JOIN
    → Keep all records from the left table.

    RIGHT JOIN
    → Keep all records from the right table.

    FULL OUTER JOIN
    → Keep all records from both tables.

    SELF JOIN
    → Join a table with itself.

    ON
    → Defines the relationship between tables.

    UNION
    → Combine query results and remove duplicates.

    UNION ALL
    → Combine query results and keep duplicates.

    INTERSECT
    → Find common rows.

    EXCEPT
    → Find rows present in the first result but not the second.

    Multiple JOINs
    → Connect several related tables through their keys.

The key mental model is:

    JOIN
    → Side by side
    → Combines columns
    → Based on relationships

    SET OPERATIONS
    → One after another
    → Combines rows
    → Based on compatible SELECT results

Once you understand the relationship chain, joining multiple tables becomes much easier:

    Customers
        ↓
    Orders
        ↓
    Products
        ↓
    Categories

And the SQL query follows the same chain:

    SELECT ...
    FROM customers
    JOIN orders
        ON ...
    JOIN products
        ON ...
    JOIN categories
        ON ...;

This is one of the most important SQL concepts for data analysis because real-world databases usually distribute information across multiple related tables rather than storing everything in one single table.