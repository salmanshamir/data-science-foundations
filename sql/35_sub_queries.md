# SQL Subqueries

## 1. What is a Subquery?

A **subquery** is a SQL query written inside another SQL query.

There are two parts:

- **Outer Query** → The main query.
- **Inner Query / Subquery** → The query written inside the outer query.

Basic structure:

    SELECT ...
    FROM ...
    WHERE column = (
        SELECT ...
    );

The query inside the parentheses is the **subquery**.

Think of it like this:

    Outer Query
        ↓
    Subquery
        ↓
    Subquery produces a result
        ↓
    Outer Query uses that result

### Simple Example

Suppose we have an `employees` table:

| employee_id | name | salary |
|---:|---|---:|
| 1 | Salman | 50000 |
| 2 | Ali | 70000 |
| 3 | Ahmed | 60000 |
| 4 | Hamza | 90000 |

Question:

> Find employees whose salary is greater than the average salary.

First, calculate the average salary:

    SELECT AVG(salary)
    FROM employees;

Suppose it returns:

    67500

We could then write:

    SELECT name, salary
    FROM employees
    WHERE salary > 67500;

But instead of manually writing `67500`, we can put the first query inside the second query:

    SELECT name, salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

Here:

    SELECT AVG(salary)
    FROM employees

is the subquery.

It produces one value, and the outer query uses that value.

Conceptually:

    Subquery:
    AVG(salary)
        ↓
    67500
        ↓
    Outer Query:
    salary > 67500


# 2. Why Do We Need Subqueries?

Subqueries are useful when the result of one query is needed by another query.

For example:

> Find employees earning more than the average salary.

You don't know the average salary beforehand.

So logically:

    First calculate average salary
            ↓
    Use that average
            ↓
    Find employees above average

A subquery allows SQL to perform these related operations in one SQL statement.

The important idea is:

> A subquery produces a result that another query uses.


# 3. General Structure of a Subquery

A subquery is normally surrounded by parentheses.

    SELECT ...
    FROM ...
    WHERE column = (
        SELECT ...
        FROM ...
    );

A subquery can appear in different parts of a SQL statement.

For example:

    SELECT *
    FROM (
        SELECT ...
        FROM ...
    ) AS temp;

The exact location determines how the subquery is being used.


# 4. Main Ways to Classify Subqueries

There are two major ways to classify subqueries.

### Based on What the Subquery Returns

1. Scalar Subquery
2. Row Subquery
3. Table Subquery

### Based on Dependency

1. Independent / Non-Correlated Subquery
2. Correlated Subquery

These classifications are very important because they tell you how the subquery behaves and how it can be used.


# 5. Scalar Subquery

A **scalar subquery returns exactly one value**.

Think of it as:

    1 row × 1 column

Example:

    SELECT AVG(salary)
    FROM employees;

Result:

    67500

That's one value.

Therefore, it can normally be used with operators such as:

    =
    >
    <
    >=
    <=
    <>

Example:

    SELECT name, salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

The subquery returns:

    67500

So conceptually the outer query becomes:

    SELECT name, salary
    FROM employees
    WHERE salary > 67500;


# 6. Scalar Subquery Example

Find the employee with the highest salary:

    SELECT name, salary
    FROM employees
    WHERE salary = (
        SELECT MAX(salary)
        FROM employees
    );

The subquery:

    SELECT MAX(salary)
    FROM employees;

returns:

    90000

The outer query then finds the employee whose salary is `90000`.

Result:

| name | salary |
|---|---:|
| Hamza | 90000 |


# 7. Important Rule of Scalar Subqueries

A scalar subquery must return:

    Exactly one value

For example:

    SELECT AVG(salary)
    FROM employees;

is scalar because it returns one value.

But:

    SELECT salary
    FROM employees;

usually returns multiple rows.

Therefore, it cannot normally be used as a scalar subquery with `=`.

For example, this is problematic:

    SELECT name
    FROM employees
    WHERE salary = (
        SELECT salary
        FROM employees
    );

The inner query returns multiple salaries.

For multiple values, you normally use operators such as:

    IN
    ANY
    ALL

or another appropriate technique.


# 8. Row Subquery

A **row subquery returns one row containing multiple columns**.

For example:

    SELECT employee_id, salary
    FROM employees
    WHERE employee_id = 2;

Result:

| employee_id | salary |
|---:|---:|
| 2 | 70000 |

This represents:

    1 row × multiple columns

Conceptually:

    (2, 70000)

Example:

    SELECT *
    FROM employees
    WHERE (employee_id, salary) = (
        SELECT employee_id, salary
        FROM employees
        WHERE name = 'Ali'
    );

The subquery returns:

    (2, 70000)

The outer query compares the pair:

    employee_id = 2
    AND
    salary = 70000


# 9. Scalar vs Row Subquery

This distinction is important.

### Scalar

    1 row × 1 column

    70000

### Row

    1 row × multiple columns

    (2, 70000)

Remember:

    Scalar
    → One value

    Row
    → One complete row


# 10. Table Subquery

A **table subquery** returns multiple rows and/or multiple columns.

For example:

    SELECT employee_id, name, salary
    FROM employees
    WHERE salary > 60000;

Result:

| employee_id | name | salary |
|---:|---|---:|
| 2 | Ali | 70000 |
| 3 | Ahmed | 60000 |
| 4 | Hamza | 90000 |

This result behaves like a temporary table.

Table subqueries are particularly useful in the `FROM` clause.

Example:

    SELECT *
    FROM (
        SELECT name, salary
        FROM employees
        WHERE salary > 60000
    ) AS high_salary;

The inner query produces a temporary table-like result called `high_salary`.


# 11. Derived Table

When a subquery appears inside `FROM`, it is often called a **derived table**.

Example:

    SELECT *
    FROM (
        SELECT name, salary
        FROM employees
        WHERE salary > 60000
    ) AS high_salary;

The subquery:

    (
        SELECT name, salary
        FROM employees
        WHERE salary > 60000
    )

produces a temporary result.

Then:

    AS high_salary

gives that result a temporary name.

In MySQL, a derived table needs an alias.


# 12. Why Use a Subquery in FROM?

Suppose you want to perform another operation on an already filtered or calculated result.

Example:

    SELECT AVG(salary)
    FROM (
        SELECT salary
        FROM employees
        WHERE department_id = 10
    ) AS dept;

Conceptually:

    First:
    Get salaries from department 10

        ↓

    Then:
    Calculate their average

The subquery creates an intermediate dataset.


# 13. Three Main Return Types

| Type | Returns | Example |
|---|---|---|
| Scalar | One value | `70000` |
| Row | One row, multiple columns | `(2, 70000)` |
| Table | Multiple rows/columns | A result set |

Mental model:

    Scalar
    ↓
    One value

    Row
    ↓
    One row

    Table
    ↓
    Many rows / columns


# 14. Independent / Non-Correlated Subquery

An **independent subquery** is also called:

- Non-correlated subquery
- Uncorrelated subquery

It can execute independently of the outer query.

Example:

    SELECT name, salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

The subquery:

    SELECT AVG(salary)
    FROM employees;

does not need anything from the outer query.

It can run independently.

Therefore, it is an independent or non-correlated subquery.


# 15. How an Independent Subquery Works

Conceptually:

    Step 1
    Run subquery
        ↓
    AVG(salary) = 67500

    Step 2
    Run outer query
        ↓
    salary > 67500

    Step 3
    Return matching employees

The subquery does not depend on individual rows from the outer query.


# 16. Correlated Subquery

A **correlated subquery depends on the outer query**.

This is one of the most important subquery concepts.

Suppose we have:

### Employees

| id | name | department_id | salary |
|---:|---|---:|---:|
| 1 | Salman | 10 | 60000 |
| 2 | Ali | 10 | 80000 |
| 3 | Ahmed | 20 | 50000 |
| 4 | Hamza | 20 | 70000 |

Question:

> Find employees whose salary is greater than the average salary of their own department.

Query:

    SELECT e.name,
           e.salary,
           e.department_id
    FROM employees AS e
    WHERE e.salary > (
        SELECT AVG(e2.salary)
        FROM employees AS e2
        WHERE e2.department_id = e.department_id
    );

Look carefully at:

    WHERE e2.department_id = e.department_id

`e.department_id` comes from the outer query.

Therefore, the inner query depends on the outer query.

That's why it is correlated.


# 17. How a Correlated Subquery Works

Conceptually:

    Outer row 1
        ↓
    Use its department_id
        ↓
    Calculate average salary of that department
        ↓
    Compare employee salary

    Outer row 2
        ↓
    Use its department_id
        ↓
    Calculate average salary of that department
        ↓
    Compare employee salary

    Outer row 3
        ↓
    ...

This is the conceptual model for understanding correlation.

The database optimizer may execute the query differently internally, but the dependency relationship remains the same.


# 18. Independent vs Correlated

| Feature | Independent | Correlated |
|---|---|---|
| Depends on outer query? | No | Yes |
| Can run independently? | Yes | No |
| References outer query? | No | Yes |
| Conceptual dependency | Independent | Depends on outer row |
| Difficulty | Easier | More advanced |

Easy memory:

    Independent
    → Inner query doesn't care about outer query.

    Correlated
    → Inner query needs information from outer query.


# 19. Subquery with SELECT

A subquery can be used inside `SELECT`.

Example:

    SELECT name,
           salary,
           (
               SELECT AVG(salary)
               FROM employees
           ) AS average_salary
    FROM employees;

Result conceptually:

| name | salary | average_salary |
|---|---:|---:|
| Salman | 60000 | 65000 |
| Ali | 80000 | 65000 |
| Ahmed | 50000 | 65000 |
| Hamza | 70000 | 65000 |

The scalar subquery returns one value:

    65000

That value is displayed for every employee.


# 20. Why SELECT Subqueries Usually Need to Be Scalar

Suppose:

    SELECT name,
           (
               SELECT salary
               FROM employees
           )
    FROM employees;

The inner query returns multiple salaries.

A single expression in the `SELECT` list cannot normally accept multiple rows from a scalar subquery.

Therefore, when using a subquery directly as a `SELECT` expression, it generally needs to return one value.


# 21. Correlated Subquery with SELECT

You can also have a correlated subquery in `SELECT`.

Example:

    SELECT e.name,
           e.department_id,
           (
               SELECT AVG(e2.salary)
               FROM employees AS e2
               WHERE e2.department_id = e.department_id
           ) AS department_average
    FROM employees AS e;

For every employee, the subquery calculates the average salary of that employee's department.

Result conceptually:

| name | department_id | department_average |
|---|---:|---:|
| Salman | 10 | 70000 |
| Ali | 10 | 70000 |
| Ahmed | 20 | 60000 |
| Hamza | 20 | 60000 |


# 22. Subquery with FROM

A subquery can appear inside `FROM`.

Example:

    SELECT *
    FROM (
        SELECT name, salary
        FROM employees
        WHERE salary > 60000
    ) AS high_salary;

The inner query creates a temporary result.

Think:

    Inner query
        ↓
    Temporary table-like result
        ↓
    Outer query

This is called a **derived table**.


# 23. Subquery with INSERT

Subqueries can be used with `INSERT`.

Suppose we have:

    employees

and:

    high_salary_employees

We want to copy employees whose salary is above 70000.

    INSERT INTO high_salary_employees
        (employee_id, name, salary)
    SELECT employee_id, name, salary
    FROM employees
    WHERE salary > 70000;

This pattern is commonly called:

    INSERT ... SELECT

Conceptually:

    SELECT data
        ↓
    Result
        ↓
    INSERT into another table


# 24. INSERT with a Scalar Subquery

You can also use a subquery to determine a value that should be inserted.

Example:

    INSERT INTO employee_summary
        (employee_id, average_salary)
    SELECT e.employee_id,
           (
               SELECT AVG(e2.salary)
               FROM employees AS e2
           )
    FROM employees AS e;

The scalar subquery calculates the average salary.


# 25. Subquery with UPDATE

Subqueries can be useful with `UPDATE`.

Suppose you want to increase the salary of employees who earn below the average salary.

Conceptually:

    UPDATE employees
    SET salary = salary * 1.10
    WHERE salary < (
        SELECT AVG(salary)
        FROM employees
    );

The logic is:

    Find average salary
        ↓
    Find employees below average
        ↓
    Increase their salary by 10%

However, when updating and selecting from the same table in MySQL, some forms can produce the error related to modifying and selecting from the same table. A derived-table workaround may be needed depending on the exact statement.

For learning the concept, remember:

    UPDATE
    +
    Subquery
    →
    Use one query's result to decide which rows or values should be updated.


# 26. UPDATE Using a Subquery from Another Table

Suppose we have:

### employees

| employee_id | name | department_id | salary |
|---:|---|---:|---:|

### department_bonus

| department_id | bonus |
|---:|---:|

You can use a subquery to determine which employees belong to departments receiving a certain bonus.

    UPDATE employees
    SET salary = salary * 1.10
    WHERE department_id IN (
        SELECT department_id
        FROM department_bonus
        WHERE bonus > 5000
    );

The subquery returns multiple department IDs.

Therefore, we use:

    IN

instead of:

    =


# 27. Subquery with DELETE

Subqueries can also be used with `DELETE`.

Suppose we want to delete employees belonging to inactive departments.

    DELETE FROM employees
    WHERE department_id IN (
        SELECT department_id
        FROM departments
        WHERE status = 'inactive'
    );

Conceptually:

    Subquery
        ↓
    Find inactive department IDs
        ↓
    Outer DELETE
        ↓
    Delete employees belonging to those departments


# 28. DELETE with NOT EXISTS

Another powerful pattern:

> Delete customers who have never placed an order.

    DELETE FROM customers AS c
    WHERE NOT EXISTS (
        SELECT 1
        FROM orders AS o
        WHERE o.customer_id = c.customer_id
    );

This is a correlated subquery because:

    c.customer_id

comes from the outer `DELETE`.

The subquery checks whether an order exists for each customer.


# 29. Subquery with WHERE

The `WHERE` clause is one of the most common places for subqueries.

Example:

    SELECT name, salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

The subquery produces a value.

The `WHERE` clause uses that value to filter rows.


# 30. Subquery with IN

`IN` is useful when the subquery returns multiple values.

Suppose:

    SELECT department_id
    FROM departments
    WHERE city = 'Karachi';

returns:

    10
    20
    30

Then:

    SELECT name, department_id
    FROM employees
    WHERE department_id IN (
        SELECT department_id
        FROM departments
        WHERE city = 'Karachi'
    );

The subquery returns multiple values.

Therefore:

    IN

is appropriate.


# 31. Why `=` and `IN` Are Different

This is extremely important.

If the subquery returns one value:

    70000

you can use:

    WHERE salary = (
        SELECT MAX(salary)
        FROM employees
    );

But if the subquery returns:

    10
    20
    30

you cannot normally use:

    WHERE department_id = (
        SELECT department_id
        FROM departments
    );

because the subquery returns multiple values.

Instead:

    WHERE department_id IN (
        SELECT department_id
        FROM departments
    );

Remember:

    =
    → One value

    IN
    → Multiple possible values


# 32. Subquery with ANY

`ANY` compares a value with the values returned by a subquery.

Example:

    SELECT name, salary
    FROM employees
    WHERE salary > ANY (
        SELECT salary
        FROM employees
        WHERE department_id = 20
    );

Meaning:

> Find employees whose salary is greater than at least one salary from department 20.

Suppose department 20 salaries are:

    50000
    60000
    70000

Then:

    salary > ANY(...)

means the salary must be greater than at least one of those values.

For example:

    55000 > 50000

is true.

Therefore, `55000` satisfies the `> ANY` condition.


# 33. Subquery with ALL

`ALL` means the condition must be true for every value returned by the subquery.

Example:

    SELECT name, salary
    FROM employees
    WHERE salary > ALL (
        SELECT salary
        FROM employees
        WHERE department_id = 20
    );

If department 20 salaries are:

    50000
    60000
    70000

then the employee must have:

    salary > 50000
    AND
    salary > 60000
    AND
    salary > 70000

Therefore, practically:

    salary > 70000

Remember:

    ANY
    → At least one

    ALL
    → Every one


# 34. ANY vs ALL

| Operator | Meaning |
|---|---|
| `ANY` | Condition true for at least one value |
| `ALL` | Condition true for every value |

Mental model:

    ANY
    → At least one

    ALL
    → Every one


# 35. Subquery with EXISTS

`EXISTS` checks whether the subquery returns at least one row.

Example:

> Find customers who have at least one order.

    SELECT c.customer_id,
           c.customer_name
    FROM customers AS c
    WHERE EXISTS (
        SELECT 1
        FROM orders AS o
        WHERE o.customer_id = c.customer_id
    );

We don't actually care what value the subquery returns.

We only care:

    Does a matching row exist?

If yes:

    EXISTS = TRUE

If no:

    EXISTS = FALSE


# 36. Why `SELECT 1` with EXISTS?

You will often see:

    SELECT 1
    FROM orders
    WHERE ...

inside `EXISTS`.

That's because `EXISTS` doesn't care about the actual selected value.

It only checks whether at least one row exists.

So:

    SELECT 1

is simply a conventional way to say:

> I only care whether a matching row exists.


# 37. NOT EXISTS

`NOT EXISTS` does the opposite.

Example:

> Find customers who have never placed an order.

    SELECT c.customer_id,
           c.customer_name
    FROM customers AS c
    WHERE NOT EXISTS (
        SELECT 1
        FROM orders AS o
        WHERE o.customer_id = c.customer_id
    );

Meaning:

    For each customer:

    Is there an order?
        ↓
    YES → Don't return customer
    NO  → Return customer


# 38. EXISTS vs IN

Both can sometimes solve similar problems, but their logic is different.

### IN

You compare against a set of values:

    WHERE department_id IN (
        SELECT department_id
        FROM departments
    );

Think:

    Is my value inside this set of values?

### EXISTS

You check whether a matching row exists:

    WHERE EXISTS (
        SELECT 1
        FROM departments AS d
        WHERE d.department_id = e.department_id
    );

Think:

    Does a matching row exist?

So:

    IN
    → Is my value in this set?

    EXISTS
    → Does a matching row exist?


# 39. Subquery with HAVING

A subquery can also be used inside `HAVING`.

Suppose:

> Find departments whose average salary is greater than the overall average salary.

    SELECT department_id,
           AVG(salary) AS department_average
    FROM employees
    GROUP BY department_id
    HAVING AVG(salary) > (
        SELECT AVG(salary)
        FROM employees
    );

The subquery:

    SELECT AVG(salary)
    FROM employees;

returns one scalar value.

Then `HAVING` compares each department's average salary with the overall average salary.


# 40. WHERE vs HAVING with Subqueries

Remember:

### WHERE

Filters individual rows.

Example:

    SELECT name, salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

### HAVING

Filters groups after `GROUP BY`.

Example:

    SELECT department_id,
           AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
    HAVING AVG(salary) > (
        SELECT AVG(salary)
        FROM employees
    );

Mental model:

    WHERE
    → Filter rows

    HAVING
    → Filter groups


# 41. Subquery with FROM vs WHERE

This distinction is important.

### Subquery in WHERE

Usually used to provide a value or set of values for filtering.

    SELECT *
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

### Subquery in FROM

Used to create an intermediate table-like result.

    SELECT *
    FROM (
        SELECT *
        FROM employees
        WHERE salary > 60000
    ) AS high_salary;

Remember:

    WHERE subquery
    → Helps decide which rows to keep.

    FROM subquery
    → Creates a temporary result to query.


# 42. Subquery with SELECT vs FROM

### SELECT

Usually returns one value for each outer row.

    SELECT name,
           (
               SELECT AVG(salary)
               FROM employees
           ) AS avg_salary
    FROM employees;

### FROM

Returns a table-like result.

    SELECT *
    FROM (
        SELECT name, salary
        FROM employees
    ) AS temp;

Remember:

    SELECT subquery
    → Usually scalar

    FROM subquery
    → Derived table


# 43. Subquery with INSERT, UPDATE and DELETE

Think about these three like this.

### INSERT

Use a query result to determine what data should be inserted.

    INSERT INTO high_salary_employees
    SELECT *
    FROM employees
    WHERE salary > 70000;

### UPDATE

Use a subquery to determine which rows or values should be updated.

    UPDATE employees
    SET salary = salary * 1.10
    WHERE department_id IN (
        SELECT department_id
        FROM departments
        WHERE status = 'active'
    );

### DELETE

Use a subquery to determine which rows should be deleted.

    DELETE FROM employees
    WHERE department_id IN (
        SELECT department_id
        FROM departments
        WHERE status = 'inactive'
    );


# 44. Nested Subqueries

A subquery can contain another subquery.

Example:

    SELECT name, salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
        WHERE department_id IN (
            SELECT department_id
            FROM departments
            WHERE city = 'Karachi'
        )
    );

Here we have:

    Outer Query
        ↓
    Subquery
        ↓
    Another Subquery

This is called a **nested subquery**.

You should understand the concept, but avoid making queries unnecessarily deeply nested because they can become difficult to understand and maintain.


# 45. Multiple Subqueries

A query can contain multiple subqueries.

Example:

    SELECT name, salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    )
    AND department_id IN (
        SELECT department_id
        FROM departments
        WHERE city = 'Karachi'
    );

Here we have two subqueries.

Subquery 1:

    SELECT AVG(salary)
    FROM employees;

It finds the average salary.

Subquery 2:

    SELECT department_id
    FROM departments
    WHERE city = 'Karachi';

It finds department IDs located in Karachi.

The outer query uses both results.


# 46. Subquery vs JOIN

Suppose we want employees from departments located in Karachi.

Using a subquery:

    SELECT name
    FROM employees
    WHERE department_id IN (
        SELECT department_id
        FROM departments
        WHERE city = 'Karachi'
    );

Using a JOIN:

    SELECT e.name
    FROM employees AS e
    INNER JOIN departments AS d
        ON e.department_id = d.department_id
    WHERE d.city = 'Karachi';

Both can solve the problem.

Why learn both?

Because they express different ideas.

    JOIN
    → Combine related data.

    Subquery
    → Use the result of one query inside another query.

In real SQL, choosing between them can depend on readability, database optimizer behavior, indexes, and the exact problem.


# 47. Subquery vs JOIN: Mental Model

Use a JOIN when you think:

> I need information from another table.

Use a subquery when you think:

> I need the result of another query to help me make a decision.

For example:

    "Show customer name and order amount"
    → JOIN is natural

    "Show employees earning above the average salary"
    → Subquery is very natural

This does not mean one method is always better. Both are important SQL techniques.


# 48. Common Mistake: Returning Too Many Rows

One of the most common beginner mistakes is using a scalar operator with a multi-row subquery.

Problematic:

    SELECT name
    FROM employees
    WHERE salary = (
        SELECT salary
        FROM employees
    );

The inner query returns multiple salaries.

But `=` expects one value.

Possible solution:

    WHERE salary IN (
        SELECT salary
        FROM employees
    );

Or if you actually need one value:

    WHERE salary = (
        SELECT MAX(salary)
        FROM employees
    );

Remember:

    =
    → Normally expects one value

    IN
    → Can work with multiple values


# 49. Common Mistake: Returning Too Many Columns

Suppose:

    WHERE salary = (
        SELECT employee_id, salary
        FROM employees
    );

This doesn't work because the scalar comparison expects one value, but the subquery returns two columns.

Remember:

    Scalar comparison
    → One value
    → One column

A row comparison can compare multiple columns:

    WHERE (employee_id, salary) = (
        SELECT employee_id, salary
        FROM employees
        WHERE name = 'Ali'
    );


# 50. Common Mistake: Forgetting the Alias in FROM Subquery

Problematic:

    SELECT *
    FROM (
        SELECT name, salary
        FROM employees
    );

In MySQL, a derived table needs an alias.

Correct:

    SELECT *
    FROM (
        SELECT name, salary
        FROM employees
    ) AS temp;

Remember:

    FROM (subquery)
    → Give it an alias.


# 51. Common Mistake: Confusing Correlated and Independent

Look at:

    SELECT name
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

The subquery doesn't reference the outer query.

Therefore:

    Non-correlated / Independent

Now:

    SELECT e.name
    FROM employees AS e
    WHERE e.salary > (
        SELECT AVG(e2.salary)
        FROM employees AS e2
        WHERE e2.department_id = e.department_id
    );

The subquery references:

    e.department_id

from the outer query.

Therefore:

    Correlated

The key question is:

> Does the inner query use a value from the outer query?

If:

    NO
    → Independent

    YES
    → Correlated


# 52. Correlated Subquery with EXISTS

This is one of the most common correlated patterns.

Question:

> Find employees who have at least one project.

    SELECT e.employee_id,
           e.name
    FROM employees AS e
    WHERE EXISTS (
        SELECT 1
        FROM employee_projects AS ep
        WHERE ep.employee_id = e.employee_id
    );

The important part is:

    ep.employee_id = e.employee_id

The `e.employee_id` belongs to the outer query.

Therefore, the subquery is correlated.


# 53. Correlated Subquery with NOT EXISTS

Question:

> Find employees who have no projects.

    SELECT e.employee_id,
           e.name
    FROM employees AS e
    WHERE NOT EXISTS (
        SELECT 1
        FROM employee_projects AS ep
        WHERE ep.employee_id = e.employee_id
    );

Conceptually:

    For every employee
        ↓
    Look for a project
        ↓
    If project exists → exclude
        ↓
    If project doesn't exist → include


# 54. Correlated Subquery for Maximum Within Group

Question:

> Find the highest-paid employee in each department.

One approach using a correlated subquery:

    SELECT e.name,
           e.department_id,
           e.salary
    FROM employees AS e
    WHERE e.salary = (
        SELECT MAX(e2.salary)
        FROM employees AS e2
        WHERE e2.department_id = e.department_id
    );

For every employee, the subquery finds:

    Maximum salary of that employee's department

Then the outer query compares the employee's salary against it.

This is a very important real-world pattern.


# 55. Subquery Categories — Complete Picture

You should understand two major classification systems.

## Based on Return Value

    Scalar Subquery
    → One value

    Row Subquery
    → One row, multiple columns

    Table Subquery
    → Multiple rows/columns

## Based on Dependency

    Independent / Non-correlated
    → Doesn't depend on outer query

    Correlated
    → Depends on outer query

These classifications can overlap.

For example:

    Scalar + Independent

    Scalar + Correlated

    Table + Independent

    Table + Correlated

So "scalar" and "correlated" are not competing categories.

They describe different properties of the same subquery.


# 56. Where Can Subqueries Be Used?

The major places you should know are:

    SELECT
    FROM
    WHERE
    HAVING
    INSERT
    UPDATE
    DELETE

They can also be used with operators such as:

    IN
    EXISTS
    NOT EXISTS
    ANY
    ALL

And subqueries can be nested inside other subqueries.


# 57. Complete Decision Process for Subqueries

When you encounter a SQL problem, ask these questions.

## Question 1 — Do I need information from another query?

If no:

    Use normal SQL.

If yes:

    Consider a subquery.


## Question 2 — What does my subquery return?

    One value
    → Scalar

    One row with multiple columns
    → Row

    Multiple rows / columns
    → Table


## Question 3 — Does my subquery need a value from the outer query?

    No
    → Independent / Non-correlated

    Yes
    → Correlated


## Question 4 — Where should I use the subquery?

    SELECT
    → Calculated value

    FROM
    → Temporary/derived table

    WHERE
    → Filter rows

    HAVING
    → Filter groups

    INSERT
    → Data to insert

    UPDATE
    → Rows/values to update

    DELETE
    → Rows to delete


# 58. Important Operators with Subqueries

## `=`

Usually for one value:

    WHERE salary = (
        SELECT MAX(salary)
        FROM employees
    );

## `IN`

For a set of values:

    WHERE department_id IN (
        SELECT department_id
        FROM departments
    );

## `EXISTS`

Check whether at least one matching row exists:

    WHERE EXISTS (
        SELECT 1
        FROM orders
        WHERE orders.customer_id = customers.customer_id
    );

## `NOT EXISTS`

Check whether no matching row exists:

    WHERE NOT EXISTS (
        SELECT 1
        FROM orders
        WHERE orders.customer_id = customers.customer_id
    );

## `ANY`

Condition is true for at least one returned value:

    WHERE salary > ANY (
        SELECT salary
        FROM employees
        WHERE department_id = 20
    );

## `ALL`

Condition is true for every returned value:

    WHERE salary > ALL (
        SELECT salary
        FROM employees
        WHERE department_id = 20
    );


# 59. Real-World Example 1 — Above Average

Problem:

> Find employees earning more than the company average.

    SELECT name, salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

Type:

    Scalar
    +
    Independent
    +
    WHERE
    +
    Aggregate


# 60. Real-World Example 2 — Employees in Karachi Departments

Problem:

> Find employees who work in departments located in Karachi.

    SELECT name, department_id
    FROM employees
    WHERE department_id IN (
        SELECT department_id
        FROM departments
        WHERE city = 'Karachi'
    );

Type:

    Multiple-row result
    +
    Independent
    +
    WHERE
    +
    IN


# 61. Real-World Example 3 — Customers with Orders

Problem:

> Find customers who have at least one order.

    SELECT c.customer_id,
           c.customer_name
    FROM customers AS c
    WHERE EXISTS (
        SELECT 1
        FROM orders AS o
        WHERE o.customer_id = c.customer_id
    );

Type:

    Correlated
    +
    EXISTS
    +
    WHERE


# 62. Real-World Example 4 — Departments Above Company Average

Problem:

> Find departments whose average salary is greater than the company average salary.

    SELECT department_id,
           AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
    HAVING AVG(salary) > (
        SELECT AVG(salary)
        FROM employees
    );

Type:

    Scalar
    +
    Independent
    +
    HAVING
    +
    Aggregate


# 63. Real-World Example 5 — Highest-Paid Employee in Each Department

Problem:

> Find the highest-paid employee in each department.

    SELECT e.name,
           e.department_id,
           e.salary
    FROM employees AS e
    WHERE e.salary = (
        SELECT MAX(e2.salary)
        FROM employees AS e2
        WHERE e2.department_id = e.department_id
    );

Type:

    Scalar
    +
    Correlated
    +
    WHERE
    +
    Aggregate


# 64. Subquery Execution Concept

For a simple independent subquery:

    SELECT name
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

Think conceptually:

    1. Execute the subquery
           ↓
    2. Get the average salary
           ↓
    3. Outer query uses that value
           ↓
    4. Filter employees
           ↓
    5. Return result

For a correlated subquery:

    1. Consider an outer row
           ↓
    2. Subquery uses information from that row
           ↓
    3. Produce a result
           ↓
    4. Outer condition is evaluated
           ↓
    5. Continue through the outer result

Important:

This is a **conceptual learning model**.

The database optimizer may execute the query differently internally.


# 65. Subquery vs Nested Query

These terms are often used interchangeably.

    Subquery
    Nested query
    Inner query

Usually they refer to the same general concept:

> A query inside another query.

For example:

    SELECT *
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

The inner `SELECT` is the subquery or nested query.


# 66. Subquery and Aggregation

Subqueries are especially useful with aggregate functions.

Common functions include:

    AVG()
    SUM()
    COUNT()
    MIN()
    MAX()

Example:

    SELECT name, salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

Here:

    AVG()
    → Produces one scalar value

Another example:

    SELECT name, salary
    FROM employees
    WHERE salary = (
        SELECT MAX(salary)
        FROM employees
    );

Here:

    MAX()
    → Produces one scalar value

This is one reason scalar subqueries are so common.


# 67. Subquery and GROUP BY

Subqueries can work together with `GROUP BY`.

Example:

    SELECT department_id,
           AVG(salary) AS department_average
    FROM employees
    GROUP BY department_id
    HAVING AVG(salary) > (
        SELECT AVG(salary)
        FROM employees
    );

The outer query creates department groups.

Then `HAVING` compares each department's average against the overall company average.

Conceptually:

    Employees
       ↓
    GROUP BY department
       ↓
    Calculate department average
       ↓
    Compare with subquery result
       ↓
    HAVING filters departments


# 68. Subquery with Multiple Conditions

You can use more than one subquery in the same query.

Example:

    SELECT name, salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    )
    AND department_id IN (
        SELECT department_id
        FROM departments
        WHERE city = 'Karachi'
    );

The first subquery answers:

    What is the average salary?

The second subquery answers:

    Which departments are in Karachi?

The outer query combines both conditions.


# 69. Subquery with Different Query Types

Subqueries are not limited to `SELECT`.

They can help with:

    SELECT
    FROM
    WHERE
    HAVING
    INSERT
    UPDATE
    DELETE

Conceptually:

    SELECT
    → Get calculated information

    FROM
    → Create derived table

    WHERE
    → Filter rows

    HAVING
    → Filter groups

    INSERT
    → Determine data to insert

    UPDATE
    → Determine rows/values to update

    DELETE
    → Determine rows to delete


# 70. A Complete Mental Map

Think about subqueries like this:

    SUBQUERY
        |
        +----------------------+
        |                      |
    What does it return?   Does it depend?
        |                      |
    +---+---+              +---+---+
    |   |   |              |       |
    |   |   |              |       |
    Scalar Row Table   Independent Correlated

Then think about where you can use them:

    SUBQUERY
       |
       +-- SELECT
       |
       +-- FROM
       |
       +-- WHERE
       |
       +-- HAVING
       |
       +-- INSERT
       |
       +-- UPDATE
       |
       +-- DELETE

And operators:

    =
    >
    <
    >=
    <=
    IN
    EXISTS
    NOT EXISTS
    ANY
    ALL


# 71. Final Decision Framework

When solving a SQL problem, follow this process:

    STEP 1
    Do I need the result of another query?
            ↓
           YES
            ↓

    STEP 2
    What does the subquery return?
            ↓

       +----------+----------+
       |          |          |
    One value   One row    Many rows
       |          |          |
    Scalar       Row      Table/Set


Then:

    STEP 3
    Does the subquery reference the outer query?
            ↓

       +----------+
       |          |
      NO         YES
       |          |
    Independent Correlated


Then:

    STEP 4
    Where should the result be used?

    SELECT
    → Calculate/provide a value

    FROM
    → Create a temporary table-like result

    WHERE
    → Filter rows

    HAVING
    → Filter groups

    INSERT
    → Select data to insert

    UPDATE
    → Determine rows/values to update

    DELETE
    → Determine rows to delete


Finally:

    STEP 5
    How should I compare/use the result?

    One value
    → =, >, <, >=, <=

    Multiple values
    → IN

    Check whether matching rows exist
    → EXISTS

    Check whether matching rows don't exist
    → NOT EXISTS

    At least one value satisfies condition
    → ANY

    Every value satisfies condition
    → ALL


# 72. Final Cheat Sheet

| Concept | Meaning |
|---|---|
| Subquery | Query inside another query |
| Outer query | Main query |
| Inner query | Query inside outer query |
| Scalar subquery | Returns one value |
| Row subquery | Returns one row |
| Table subquery | Returns multiple rows/columns |
| Independent subquery | Doesn't depend on outer query |
| Correlated subquery | Depends on outer query |
| Derived table | Subquery inside `FROM` |
| `IN` | Compare against multiple values |
| `EXISTS` | Check whether rows exist |
| `NOT EXISTS` | Check whether rows don't exist |
| `ANY` | Condition true for at least one value |
| `ALL` | Condition true for every value |


# 73. The Most Important Mental Model

If you remember only one thing about subqueries, remember:

    SUBQUERY
    =
    A query whose result is used by another query.

Then ask:

    WHAT DOES IT RETURN?

    One value
    → Scalar

    One row
    → Row

    Multiple rows/columns
    → Table


Then ask:

    DOES IT DEPEND ON THE OUTER QUERY?

    No
    → Independent / Non-correlated

    Yes
    → Correlated


Then ask:

    WHERE AM I USING IT?

    SELECT
    → Calculate/provide a value

    FROM
    → Create a temporary table-like result

    WHERE
    → Filter rows

    HAVING
    → Filter groups

    INSERT
    → Select data to insert

    UPDATE
    → Determine rows/values to update

    DELETE
    → Determine rows to delete


And finally:

    HOW DO I USE THE RESULT?

    One value
    → =, >, <, >=, <=

    Multiple values
    → IN

    Does a matching row exist?
    → EXISTS

    Does no matching row exist?
    → NOT EXISTS

    At least one value satisfies condition
    → ANY

    Every value satisfies condition
    → ALL


# 74. Final Summary

The complete mental model of SQL subqueries is:

    Query inside another query
            ↓
    SUBQUERY
            ↓
    ┌─────────────────────────────────┐
    │                                 │
    │ What does it return?            │
    │                                 │
    │ Scalar → One value              │
    │ Row    → One row                │
    │ Table  → Multiple rows/columns  │
    └─────────────────────────────────┘
            ↓
    ┌─────────────────────────────────┐
    │                                 │
    │ Does it depend on outer query?  │
    │                                 │
    │ No  → Independent               │
    │ Yes → Correlated                │
    └─────────────────────────────────┘
            ↓
    ┌─────────────────────────────────┐
    │                                 │
    │ Where is it used?               │
    │                                 │
    │ SELECT → Value                  │
    │ FROM   → Derived table          │
    │ WHERE  → Row filtering          │
    │ HAVING → Group filtering        │
    │ INSERT → Data to insert         │
    │ UPDATE → Data to update         │
    │ DELETE → Data to delete         │
    └─────────────────────────────────┘
            ↓
    ┌─────────────────────────────────┐
    │                                 │
    │ How is result used?             │
    │                                 │
    │ =       → One value             │
    │ IN      → Multiple values       │
    │ EXISTS  → Matching row exists   │
    │ NOT EXISTS → No matching row   │
    │ ANY     → At least one          │
    │ ALL     → Every value           │
    └─────────────────────────────────┘

The central idea is:

> **One query produces a result, and another query uses that result.**

Once this mental model is clear, subqueries stop being a collection of random SQL tricks and become one consistent SQL concept.