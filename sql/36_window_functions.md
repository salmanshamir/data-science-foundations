# SQL Window Functions

Window functions are one of the most important advanced SQL concepts. They allow us to perform calculations across related rows **without collapsing those rows into a single row**.

This chapter covers:

* What are Window Functions?
* How Window Functions work internally
* `OVER()`
* `PARTITION BY`
* `ORDER BY` inside `OVER()`
* Window vs `GROUP BY`
* `ROW_NUMBER()`
* `RANK()`
* `DENSE_RANK()`
* `FIRST_VALUE()`
* `LAST_VALUE()`
* `NTH_VALUE()`
* `LAG()`
* `LEAD()`
* Window Frames
* `ROWS`
* `UNBOUNDED PRECEDING`
* `CURRENT ROW`
* `UNBOUNDED FOLLOWING`
* Practical examples
* Differences between Window Functions

---

# 1. What is a Window Function?

A **Window Function** performs a calculation across a set of rows that are related to the current row.

The most important point is:

> A window function performs a calculation over multiple rows while keeping the original rows in the result.

This is different from `GROUP BY`.

For example:

```sql
SELECT
    Sport,
    AVG(Weight)
FROM athlete
GROUP BY Sport;
```

If the table contains:

| Sport    | Weight |
| -------- | -----: |
| Swimming |     80 |
| Swimming |     82 |
| Swimming |     78 |
| Boxing   |     70 |
| Boxing   |     72 |

The result is:

| Sport    | AVG(Weight) |
| -------- | ----------: |
| Swimming |          80 |
| Boxing   |          71 |

The original athlete rows are collapsed.

---

## Window Function

Now consider:

```sql
SELECT
    Sport,
    Weight,
    AVG(Weight) OVER(
        PARTITION BY Sport
    ) AS sport_avg
FROM athlete;
```

Result:

| Sport    | Weight | sport_avg |
| -------- | -----: | --------: |
| Swimming |     80 |        80 |
| Swimming |     82 |        80 |
| Swimming |     78 |        80 |
| Boxing   |     70 |        71 |
| Boxing   |     72 |        71 |

Notice that every original row is still present.

The average is calculated over the window and then attached to every corresponding row.

---

# 2. Window Function Mental Model

Think of a window function as creating a temporary logical "window" through which SQL can look at related rows.

For example:

```text
Swimming
----------------
80
82  <- current row
78
```

For the current Swimming row, the window can contain the other Swimming rows.

For Boxing:

```text
Boxing
----------------
70  <- current row
72
```

The function performs its calculation using the rows available inside that window.

The original rows are still returned.

---

# 3. Basic Syntax

The general syntax of a window function is:

```sql
FUNCTION(...) OVER(
    PARTITION BY ...
    ORDER BY ...
)
```

For example:

```sql
AVG(Weight) OVER(
    PARTITION BY Sport
)
```

There are three major components:

```text
FUNCTION()
    ↓
OVER()
    ↓
PARTITION BY
    ↓
ORDER BY
```

However, `PARTITION BY` and `ORDER BY` are not always required.

---

# 4. OVER()

`OVER()` is what turns a normal aggregate function into a window function.

For example:

```sql
AVG(Weight)
```

is an aggregate function.

But:

```sql
AVG(Weight) OVER()
```

is a window function.

The `OVER()` tells SQL:

> Perform this calculation as a window calculation and keep the individual rows.

---

# 5. OVER() Without PARTITION BY

Consider:

```sql
SELECT
    Name,
    Weight,
    AVG(Weight) OVER() AS overall_avg
FROM athlete;
```

Here there is no `PARTITION BY`.

Therefore, the window contains the entire result set.

For example:

| Name  | Weight | overall_avg |
| ----- | -----: | ----------: |
| Ali   |     80 |          72 |
| Ahmed |     70 |          72 |
| Sara  |     65 |          72 |
| John  |     73 |          72 |

The average is calculated over the entire result set and attached to every row.

---

# 6. PARTITION BY

`PARTITION BY` divides the rows into separate logical windows.

For example:

```sql
AVG(Weight) OVER(
    PARTITION BY Sport
)
```

means:

> Create a separate window for every Sport.

Suppose we have:

```text
Swimming
80
82
78

Boxing
70
72
```

SQL logically creates:

```text
Swimming Window
----------------
80
82
78

Boxing Window
----------------
70
72
```

Then it calculates the average separately inside each window.

---

# 7. PARTITION BY Does Not Collapse Rows

This is one of the most important differences between `GROUP BY` and `PARTITION BY`.

### GROUP BY

```sql
SELECT
    Sport,
    AVG(Weight)
FROM athlete
GROUP BY Sport;
```

Produces one row per sport.

### Window Function

```sql
SELECT
    Sport,
    Weight,
    AVG(Weight) OVER(
        PARTITION BY Sport
    ) AS sport_avg
FROM athlete;
```

Keeps every athlete.

Therefore:

```text
GROUP BY
    ↓
Combines/collapses rows

PARTITION BY
    ↓
Creates separate windows but keeps rows
```

---

# 8. ORDER BY Inside OVER()

The `ORDER BY` inside the window defines the order in which rows are considered by the window function.

Example:

```sql
ROW_NUMBER() OVER(
    ORDER BY Weight
)
```

means:

> Arrange the rows according to Weight and assign row numbers in that order.

Suppose:

| Weight |
| -----: |
|     80 |
|     60 |
|     70 |
|     90 |

The window ordering becomes:

```text
60
70
80
90
```

Then `ROW_NUMBER()` assigns:

```text
60 → 1
70 → 2
80 → 3
90 → 4
```

---

# 9. Window ORDER BY vs Final ORDER BY

These two are different.

### Window ORDER BY

```sql
ROW_NUMBER() OVER(
    ORDER BY Weight
)
```

controls the order used by the window function.

### Final ORDER BY

```sql
ORDER BY Weight;
```

controls the order in which the final result is displayed.

For example:

```sql
SELECT
    Name,
    Weight,
    ROW_NUMBER() OVER(
        ORDER BY Weight
    ) AS row_num
FROM athlete
ORDER BY Name;
```

Here:

* `ROW_NUMBER()` uses `Weight`
* Final output uses `Name`

---

# 10. Window Functions We Will Learn

There are several categories.

## Ranking Functions

```text
ROW_NUMBER()
RANK()
DENSE_RANK()
```

These answer:

> What position does this row have?

## Value Functions

```text
FIRST_VALUE()
LAST_VALUE()
NTH_VALUE()
```

These answer:

> What value exists at a particular position in the window?

## Navigation Functions

```text
LAG()
LEAD()
```

These answer:

> What value exists before or after the current row?

---

# 11. ROW_NUMBER()

`ROW_NUMBER()` assigns a unique sequential number to every row.

Syntax:

```sql
ROW_NUMBER() OVER(
    ORDER BY column
)
```

Example:

```sql
SELECT
    Name,
    Weight,
    ROW_NUMBER() OVER(
        ORDER BY Weight
    ) AS row_num
FROM athlete;
```

Suppose:

| Weight |
| -----: |
|     60 |
|     65 |
|     70 |
|     75 |

Result:

| Weight | row_num |
| -----: | ------: |
|     60 |       1 |
|     65 |       2 |
|     70 |       3 |
|     75 |       4 |

---

# 12. Internal Working of ROW_NUMBER()

Conceptually SQL performs:

```text
Step 1:
Take the rows.

Step 2:
Sort them according to the window ORDER BY.

Step 3:
Start a counter at 1.

Step 4:
Assign 1 to the first row.

Step 5:
Assign 2 to the second row.

Step 6:
Continue until every row has a number.
```

For:

```text
60
60
70
70
75
```

`ROW_NUMBER()` produces:

```text
60 → 1
60 → 2
70 → 3
70 → 4
75 → 5
```

Even tied values receive different row numbers.

---

# 13. ROW_NUMBER() with PARTITION BY

We can restart the numbering for every group.

```sql
SELECT
    Name,
    Sport,
    Weight,
    ROW_NUMBER() OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS row_num
FROM athlete;
```

Suppose:

| Sport    | Weight |
| -------- | -----: |
| Swimming |     60 |
| Swimming |     70 |
| Swimming |     80 |
| Boxing   |     65 |
| Boxing   |     75 |

Result:

| Sport    | Weight | row_num |
| -------- | -----: | ------: |
| Swimming |     60 |       1 |
| Swimming |     70 |       2 |
| Swimming |     80 |       3 |
| Boxing   |     65 |       1 |
| Boxing   |     75 |       2 |

The numbering restarts at 1 for every partition.

---

# 14. RANK()

`RANK()` assigns ranks based on the ordering.

The important feature is:

> Tied rows receive the same rank, and gaps are created after ties.

Example:

```sql
SELECT
    Name,
    Weight,
    RANK() OVER(
        ORDER BY Weight
    ) AS rank_num
FROM athlete;
```

Suppose:

```text
Weight
------
60
60
70
80
80
90
```

Result:

```text
Weight    Rank
----------------
60          1
60          1
70          3
80          4
80          4
90          6
```

---

# 15. Why Does RANK() Create Gaps?

Consider a competition:

```text
Ali       100 points
Ahmed      90 points
Sara       90 points
John       80 points
```

Ranking:

```text
Ali       → 1
Ahmed     → 2
Sara      → 2
John      → 4
```

Why is John 4?

Because two people occupied rank 2.

This is called competition ranking.

The sequence is:

```text
1
2
2
4
```

---

# 16. DENSE_RANK()

`DENSE_RANK()` is similar to `RANK()`.

The difference:

> `DENSE_RANK()` does not create gaps after ties.

Example:

```sql
SELECT
    Name,
    Weight,
    DENSE_RANK() OVER(
        ORDER BY Weight
    ) AS dense_rank_num
FROM athlete;
```

For:

```text
Weight
------
60
60
70
80
80
90
```

Result:

```text
Weight    Dense Rank
--------------------
60            1
60            1
70            2
80            3
80            3
90            4
```

---

# 17. RANK() vs DENSE_RANK()

Given:

```text
Score
-----
100
90
90
80
80
70
```

### RANK()

```text
100 → 1
90  → 2
90  → 2
80  → 4
80  → 4
70  → 6
```

### DENSE_RANK()

```text
100 → 1
90  → 2
90  → 2
80  → 3
80  → 3
70  → 4
```

The easiest way to remember:

```text
RANK()
→ leaves gaps

DENSE_RANK()
→ does not leave gaps
```

---

# 18. ROW_NUMBER() vs RANK() vs DENSE_RANK()

Suppose:

```text
Score
-----
100
90
90
80
80
70
```

### ROW_NUMBER()

```text
1
2
3
4
5
6
```

Every row gets a unique number.

### RANK()

```text
1
2
2
4
4
6
```

Ties share rank and gaps appear.

### DENSE_RANK()

```text
1
2
2
3
3
4
```

Ties share rank but no gaps appear.

---

# 19. FIRST_VALUE()

`FIRST_VALUE()` returns the value from the first row in the window according to the window's ordering.

Example:

```sql
SELECT
    Name,
    Sport,
    Weight,
    FIRST_VALUE(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS first_weight
FROM athlete;
```

Suppose:

```text
Swimming
60
70
80
```

Result:

```text
Weight    first_weight
----------------------
60             60
70             60
80             60
```

The first value according to:

```sql
ORDER BY Weight
```

is `60`.

Therefore every row gets `60`.

---

# 20. FIRST_VALUE() Does Not Mean First Physical Row

This is very important.

`FIRST_VALUE()` does not mean:

> Give me the first row physically stored in the table.

It means:

> Give me the value from the first row according to the window's ordering.

For example:

```sql
FIRST_VALUE(Weight) OVER(
    ORDER BY Weight
)
```

returns the smallest weight.

While:

```sql
FIRST_VALUE(Weight) OVER(
    ORDER BY Weight DESC
)
```

returns the largest weight.

---

# 21. FIRST_VALUE() with PARTITION BY

```sql
SELECT
    Name,
    Sport,
    Weight,
    FIRST_VALUE(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS lowest_weight
FROM athlete;
```

For each sport, SQL finds the first weight according to ascending order.

Example:

```text
Swimming:
60
70
80

Boxing:
65
75
```

Result:

```text
Swimming  60 → 60
Swimming  70 → 60
Swimming  80 → 60

Boxing    65 → 65
Boxing    75 → 65
```

---

# 22. LAST_VALUE()

`LAST_VALUE()` returns the value from the last row of the window/frame.

Example:

```sql
LAST_VALUE(Weight) OVER(
    PARTITION BY Sport
    ORDER BY Weight
)
```

However, `LAST_VALUE()` is tricky because of the **window frame**.

You might expect every Swimming row to receive the largest weight:

```text
60 → 80
70 → 80
80 → 80
```

But depending on the database's default frame, you may instead see:

```text
60 → 60
70 → 70
80 → 80
```

This happens because the default frame can end at the current row.

---

# 23. What is a Window Frame?

There are two related concepts:

```text
Partition
    ↓
Frame
```

A **partition** is the complete group of related rows.

A **frame** is the subset of rows currently available to the window function for the current row.

For example:

```text
Swimming

60
70 ← current row
80
```

The partition is:

```text
60
70
80
```

But the current frame might be:

```text
60
70
```

depending on the window definition.

This is why `LAST_VALUE()` can behave differently from what beginners expect.

---

# 24. Getting the True Last Value

We can explicitly tell SQL to use the complete partition:

```sql
SELECT
    Name,
    Sport,
    Weight,
    LAST_VALUE(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
        ROWS BETWEEN
            UNBOUNDED PRECEDING
            AND UNBOUNDED FOLLOWING
    ) AS last_weight
FROM athlete;
```

Now the entire partition is included in the frame.

For:

```text
Swimming
60
70
80
```

the last value is:

```text
80
```

Every row receives:

```text
60 → 80
70 → 80
80 → 80
```

---

# 25. UNBOUNDED PRECEDING

```sql
UNBOUNDED PRECEDING
```

means:

> Start from the beginning of the partition.

---

# 26. UNBOUNDED FOLLOWING

```sql
UNBOUNDED FOLLOWING
```

means:

> Continue all the way to the end of the partition.

Therefore:

```sql
ROWS BETWEEN
    UNBOUNDED PRECEDING
    AND UNBOUNDED FOLLOWING
```

means:

> Use the entire partition as the frame.

---

# 27. NTH_VALUE()

`NTH_VALUE()` returns the value from the N-th row of the window.

Syntax:

```sql
NTH_VALUE(column, N)
```

For example:

```sql
NTH_VALUE(Weight, 2)
```

means:

> Return the value from the second row.

Suppose:

```text
Weight
------
60
70
80
90
```

Then the second value is:

```text
70
```

---

# 28. NTH_VALUE() Example

```sql
SELECT
    Name,
    Weight,
    NTH_VALUE(Weight, 2) OVER(
        ORDER BY Weight
        ROWS BETWEEN
            UNBOUNDED PRECEDING
            AND UNBOUNDED FOLLOWING
    ) AS second_weight
FROM athlete;
```

Result:

```text
Weight    second_weight
-----------------------
60             70
70             70
80             70
90             70
```

The complete frame is specified so that the second row of the entire window is available.

---

# 29. LAG()

`LAG()` allows us to access a value from a previous row.

Syntax:

```sql
LAG(column)
OVER(
    ORDER BY ...
)
```

Example:

```sql
SELECT
    Name,
    Weight,
    LAG(Weight) OVER(
        ORDER BY Weight
    ) AS previous_weight
FROM athlete;
```

Suppose:

```text
Weight
------
60
70
80
90
```

Result:

```text
Weight    previous_weight
-------------------------
60             NULL
70             60
80             70
90             80
```

---

# 30. Why is the First LAG() Value NULL?

For the first row:

```text
60
```

there is no previous row.

Therefore:

```text
LAG(60) = NULL
```

For the second row:

```text
70
```

the previous row is:

```text
60
```

Therefore:

```text
LAG(70) = 60
```

The mental model is:

```text
Current     Previous
--------------------
60          NULL
70          60
80          70
90          80
```

---

# 31. LAG() Offset

By default:

```sql
LAG(Weight)
```

looks one row backward.

You can specify the offset:

```sql
LAG(Weight, 2)
```

This means:

> Look two rows backward.

Example:

```text
Weight
------
60
70
80
90
100
```

Result:

```text
Weight    LAG(Weight,2)
-----------------------
60            NULL
70            NULL
80             60
90             70
100            80
```

---

# 32. LAG() Default Value

You can also specify a default value.

Syntax:

```sql
LAG(column, offset, default_value)
```

Example:

```sql
LAG(Weight, 1, 0)
```

This means:

```text
column = Weight
offset = 1
default = 0
```

Result:

```text
Weight    Previous
------------------
60           0
70          60
80          70
90          80
```

Instead of `NULL`, the first row gets `0`.

---

# 33. LEAD()

`LEAD()` is the opposite of `LAG()`.

`LAG()` looks backward.

`LEAD()` looks forward.

Example:

```sql
SELECT
    Name,
    Weight,
    LEAD(Weight) OVER(
        ORDER BY Weight
    ) AS next_weight
FROM athlete;
```

Suppose:

```text
Weight
------
60
70
80
90
```

Result:

```text
Weight    next_weight
---------------------
60            70
70            80
80            90
90            NULL
```

---

# 34. LAG() vs LEAD()

Think:

```text
Previous        Current        Next

   LAG  ←          ●          →  LEAD
```

For:

```text
60   70   80   90
```

when the current row is `70`:

```text
LAG  = 60
LEAD = 80
```

---

# 35. LEAD() with Offset

Just like `LAG()`:

```sql
LEAD(Weight, 2)
```

means:

> Look two rows forward.

Example:

```text
Weight
------
60
70
80
90
100
```

Result:

```text
Weight    LEAD(Weight,2)
------------------------
60             80
70             90
80             100
90             NULL
100            NULL
```

---

# 36. LEAD() with Default Value

You can also provide a default:

```sql
LEAD(Weight, 1, 0)
```

The final row will receive `0` instead of `NULL`.

---

# 37. LAG() with PARTITION BY

Consider:

```text
Sport       Weight
------------------
Swimming      60
Swimming      70
Swimming      80
Boxing        65
Boxing        75
```

Query:

```sql
SELECT
    Sport,
    Weight,
    LAG(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS previous_weight
FROM athlete;
```

Result:

```text
Sport       Weight    Previous
--------------------------------
Swimming      60       NULL
Swimming      70       60
Swimming      80       70

Boxing        65       NULL
Boxing        75       65
```

Notice that the first Boxing row does not get `80`.

Why?

Because Boxing has its own partition.

The navigation starts again inside the Boxing window.

---

# 38. LEAD() with PARTITION BY

Similarly:

```sql
SELECT
    Sport,
    Weight,
    LEAD(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS next_weight
FROM athlete;
```

For:

```text
Swimming:
60
70
80
```

we get:

```text
60 → 70
70 → 80
80 → NULL
```

For Boxing:

```text
65
75
```

we get:

```text
65 → 75
75 → NULL
```

---

# 39. Previous Row Does Not Mean Physical Previous Row

This is very important.

Consider:

```sql
LAG(Weight) OVER(
    ORDER BY Age
)
```

The "previous row" means:

> Previous row according to Age.

It does not necessarily mean:

> The row physically above it in the table.

If we use:

```sql
LAG(Weight) OVER(
    ORDER BY Weight
)
```

then "previous" means previous according to Weight.

Therefore:

```text
ORDER BY
    ↓
defines what previous and next mean
```

---

# 40. Ties and Window Ordering

Suppose:

```text
Age
---
20
20
25
30
```

If we write:

```sql
ROW_NUMBER() OVER(
    ORDER BY Age
)
```

the two age-20 rows are tied.

Their relative order is not necessarily deterministic.

If you need a predictable order, add another column:

```sql
ROW_NUMBER() OVER(
    ORDER BY Age, Name
)
```

Now SQL can use `Name` as a tie-breaker.

This is an important professional SQL practice.

---

# 41. FIRST_VALUE() vs MIN()

These can sometimes produce similar results, but they are conceptually different.

```sql
MIN(Weight) OVER(
    PARTITION BY Sport
)
```

asks:

> What is the minimum Weight?

While:

```sql
FIRST_VALUE(Weight) OVER(
    PARTITION BY Sport
    ORDER BY Weight
)
```

asks:

> What is the Weight of the first row according to this ordering?

They may produce the same result when ordering by Weight ascending, but the concepts are different.

For example:

```sql
FIRST_VALUE(Name) OVER(
    PARTITION BY Sport
    ORDER BY Weight
)
```

returns the **Name associated with the first Weight**, something `MIN(Weight)` cannot do directly.

---

# 42. FIRST_VALUE() Can Retrieve Related Values

Suppose:

| Name  | Sport    | Weight |
| ----- | -------- | -----: |
| Ali   | Swimming |     80 |
| Ahmed | Swimming |     60 |
| Sara  | Swimming |     70 |

Query:

```sql
SELECT
    Name,
    Weight,
    FIRST_VALUE(Name) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS lightest_athlete
FROM athlete;
```

The result can identify the athlete associated with the smallest weight.

The important idea is:

> `FIRST_VALUE()` retrieves the value from the row occupying the first position, not necessarily the minimum or maximum of the column.

---

# 43. Ranking Functions Category

The following functions are ranking functions:

```text
ROW_NUMBER()
RANK()
DENSE_RANK()
```

They answer:

> What position does this row have?

---

# 44. Value Functions Category

These functions retrieve values from positions in the window:

```text
FIRST_VALUE()
LAST_VALUE()
NTH_VALUE()
```

They answer:

> What value exists at a particular position?

---

# 45. Navigation Functions Category

These functions navigate relative to the current row:

```text
LAG()
LEAD()
```

They answer:

> What value is before or after the current row?

---

# 46. Complete Comparison Table

| Function        | Purpose                 | Handles Ties              | Looks Relative to Current Row? |
| --------------- | ----------------------- | ------------------------- | ------------------------------ |
| `ROW_NUMBER()`  | Gives unique row number | No tie                    | No                             |
| `RANK()`        | Gives rank with gaps    | Same rank for ties        | No                             |
| `DENSE_RANK()`  | Gives rank without gaps | Same rank for ties        | No                             |
| `FIRST_VALUE()` | Gets first value        | Depends on ordering       | No                             |
| `LAST_VALUE()`  | Gets last value         | Depends on frame/order    | No                             |
| `NTH_VALUE()`   | Gets N-th value         | Depends on ordering/frame | No                             |
| `LAG()`         | Gets previous value     | No                        | Yes                            |
| `LEAD()`        | Gets next value         | No                        | Yes                            |

---

# 47. One Example Combining Multiple Window Functions

We can use several window functions in the same query:

```sql
SELECT
    Name,
    Sport,
    Weight,

    ROW_NUMBER() OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS row_num,

    RANK() OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS rank_num,

    DENSE_RANK() OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS dense_rank_num,

    FIRST_VALUE(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS first_weight,

    LAG(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS previous_weight,

    LEAD(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS next_weight

FROM athlete;
```

This demonstrates how different window functions can use the same partition and ordering but perform different operations.

---

# 48. Conceptual Internal Working

When SQL processes something like:

```sql
AVG(Weight) OVER(
    PARTITION BY Sport
    ORDER BY Weight
)
```

a useful conceptual model is:

```text
                    TABLE
                      ↓
                   FROM
                      ↓
                  FILTERING
                      ↓
              Create partitions
                      ↓
             ┌────────┴────────┐
             ↓                 ↓
         Swimming            Boxing
             ↓                 ↓
          ORDER BY           ORDER BY
          Weight             Weight
             ↓                 ↓
        Window rows        Window rows
             ↓                 ↓
          Function            Function
          calculation         calculation
             ↓                 ↓
       Result attached   Result attached
        to each row       to each row
```

This is a learning model rather than a literal description of every physical operation performed by a database optimizer.

---

# 49. Window vs GROUP BY

This difference must become automatic in your mind.

### GROUP BY

```sql
SELECT
    Sport,
    AVG(Weight)
FROM athlete
GROUP BY Sport;
```

Conceptually:

```text
Many rows
    ↓
GROUP BY
    ↓
One row per group
```

### Window Function

```sql
SELECT
    Sport,
    Weight,
    AVG(Weight) OVER(
        PARTITION BY Sport
    )
FROM athlete;
```

Conceptually:

```text
Many rows
    ↓
Create logical partitions
    ↓
Calculate
    ↓
Keep all rows
```

---

# 50. Window Frame

A window frame defines the rows available to the window function for the current row.

A simplified mental model is:

```text
Partition
    ↓
Frame
    ↓
Function
```

For example:

```text
Partition:

60
70
80
90
```

If the current row is `80`, the frame might be:

```text
60
70
80
```

or potentially the entire partition, depending on the window specification.

This is especially important for:

```text
FIRST_VALUE()
LAST_VALUE()
NTH_VALUE()
```

and for more advanced aggregate-window calculations.

---

# 51. ROWS

`ROWS` allows you to explicitly define a frame using physical row positions.

For example:

```sql
ROWS BETWEEN
    UNBOUNDED PRECEDING
    AND UNBOUNDED FOLLOWING
```

means:

> Start from the first row and continue through the final row of the partition.

---

# 52. CURRENT ROW

```sql
CURRENT ROW
```

means:

> The current row being processed.

For example:

```sql
ROWS BETWEEN
    UNBOUNDED PRECEDING
    AND CURRENT ROW
```

means:

> Start at the beginning of the partition and continue up to the current row.

This is commonly used for running totals.

Example:

```sql
SELECT
    Year,
    Sales,
    SUM(Sales) OVER(
        ORDER BY Year
        ROWS BETWEEN
            UNBOUNDED PRECEDING
            AND CURRENT ROW
    ) AS running_total
FROM sales;
```

For:

```text
Year    Sales
------------
2021    100
2022    120
2023    150
```

the running total is:

```text
2021 → 100
2022 → 220
2023 → 370
```

---

# 53. UNBOUNDED PRECEDING

```sql
UNBOUNDED PRECEDING
```

means:

> Beginning of the partition.

---

# 54. UNBOUNDED FOLLOWING

```sql
UNBOUNDED FOLLOWING
```

means:

> End of the partition.

---

# 55. Entire Partition Frame

The following:

```sql
ROWS BETWEEN
    UNBOUNDED PRECEDING
    AND UNBOUNDED FOLLOWING
```

means:

```text
Beginning
   ↓
Every row
   ↓
End
```

In other words:

> The entire partition.

This is particularly useful with `LAST_VALUE()` and `NTH_VALUE()` when you want to make sure the entire partition is available.

---

# 56. Running Total Example

Window functions can calculate running totals without collapsing rows.

```sql
SELECT
    Year,
    Sales,
    SUM(Sales) OVER(
        ORDER BY Year
        ROWS BETWEEN
            UNBOUNDED PRECEDING
            AND CURRENT ROW
    ) AS running_total
FROM sales;
```

Result:

| Year | Sales | Running Total |
| ---- | ----: | ------------: |
| 2021 |   100 |           100 |
| 2022 |   120 |           220 |
| 2023 |   150 |           370 |
| 2024 |   130 |           500 |

The window grows as SQL moves through the ordered rows.

---

# 57. LAG() for Previous-Year Comparison

Suppose:

| Year | Sales |
| ---- | ----: |
| 2021 |   100 |
| 2022 |   120 |
| 2023 |   150 |
| 2024 |   130 |

Query:

```sql
SELECT
    Year,
    Sales,
    LAG(Sales) OVER(
        ORDER BY Year
    ) AS previous_sales
FROM sales;
```

Result:

| Year | Sales | Previous Sales |
| ---- | ----: | -------------: |
| 2021 |   100 |           NULL |
| 2022 |   120 |            100 |
| 2023 |   150 |            120 |
| 2024 |   130 |            150 |

Then we can calculate the difference:

```sql
SELECT
    Year,
    Sales,
    Sales - LAG(Sales) OVER(
        ORDER BY Year
    ) AS sales_change
FROM sales;
```

Result:

| Year | Sales | Sales Change |
| ---- | ----: | -----------: |
| 2021 |   100 |         NULL |
| 2022 |   120 |           20 |
| 2023 |   150 |           30 |
| 2024 |   130 |          -20 |

---

# 58. LEAD() for Future Comparison

```sql
SELECT
    Year,
    Sales,
    LEAD(Sales) OVER(
        ORDER BY Year
    ) AS next_sales
FROM sales;
```

Result:

| Year | Sales | Next Sales |
| ---- | ----: | ---------: |
| 2021 |   100 |        120 |
| 2022 |   120 |        150 |
| 2023 |   150 |        130 |
| 2024 |   130 |       NULL |

This is useful when you want to compare the current row with the next row.

---

# 59. Important NULL Behavior

`LAG()` and `LEAD()` naturally produce `NULL` when there is no previous or next row.

For example:

```text
LAG:

First row → NULL
```

and:

```text
LEAD:

Last row → NULL
```

You can replace this with a default:

```sql
LAG(Sales, 1, 0) OVER(
    ORDER BY Year
)
```

---

# 60. Common Mistakes

## Mistake 1: Confusing GROUP BY and PARTITION BY

Wrong mental model:

```text
PARTITION BY = GROUP BY
```

They are not the same.

`GROUP BY` collapses rows.

`PARTITION BY` creates logical windows while retaining rows.

---

## Mistake 2: Forgetting ORDER BY

For ranking and navigation functions, `ORDER BY` is usually essential.

For example:

```sql
LAG(Weight) OVER()
```

does not define what "previous" means.

Better:

```sql
LAG(Weight) OVER(
    ORDER BY Age
)
```

---

## Mistake 3: Assuming LAG means physical previous row

It means previous according to the window's `ORDER BY`.

---

## Mistake 4: Expecting RANK() to give unique numbers

It doesn't.

Tied values receive the same rank.

Use `ROW_NUMBER()` when every row needs a unique position.

---

## Mistake 5: Confusing RANK() and DENSE_RANK()

Remember:

```text
RANK
→ gaps

DENSE_RANK
→ no gaps
```

---

## Mistake 6: Forgetting the Window Frame with LAST_VALUE()

`LAST_VALUE()` can produce unexpected results if you don't understand the frame.

When you need the last value of the entire partition, explicitly specify:

```sql
ROWS BETWEEN
    UNBOUNDED PRECEDING
    AND UNBOUNDED FOLLOWING
```

---

# 61. Practical Example: Top Athletes by Sport

Suppose you want to rank athletes by weight inside every sport:

```sql
SELECT
    Name,
    Sport,
    Weight,
    RANK() OVER(
        PARTITION BY Sport
        ORDER BY Weight DESC
    ) AS weight_rank
FROM athlete;
```

Because of:

```sql
ORDER BY Weight DESC
```

the heaviest athlete gets rank 1.

Because of:

```sql
PARTITION BY Sport
```

the ranking starts again for every sport.

---

# 62. Practical Example: Number Athletes Within Each Sport

```sql
SELECT
    Name,
    Sport,
    ROW_NUMBER() OVER(
        PARTITION BY Sport
        ORDER BY Name
    ) AS athlete_number
FROM athlete;
```

This creates:

```text
Swimming
    1
    2
    3

Boxing
    1
    2
```

---

# 63. Practical Example: Previous Athlete Weight

```sql
SELECT
    Name,
    Sport,
    Weight,
    LAG(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS previous_weight
FROM athlete;
```

This tells us the weight of the previous athlete according to Weight within the same sport.

---

# 64. Practical Example: Next Athlete Weight

```sql
SELECT
    Name,
    Sport,
    Weight,
    LEAD(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS next_weight
FROM athlete;
```

This tells us the weight of the next athlete according to Weight within the same sport.

---

# 65. Practical Example: Lowest Weight in Each Sport

```sql
SELECT
    Name,
    Sport,
    Weight,
    FIRST_VALUE(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight
    ) AS lowest_weight
FROM athlete;
```

---

# 66. Practical Example: Highest Weight in Each Sport

One approach is:

```sql
SELECT
    Name,
    Sport,
    Weight,
    FIRST_VALUE(Weight) OVER(
        PARTITION BY Sport
        ORDER BY Weight DESC
    ) AS highest_weight
FROM athlete;
```

Because descending order places the largest weight first.

---

# 67. The Most Important Mental Model

Whenever you see:

```sql
FUNCTION(...) OVER(
    PARTITION BY something
    ORDER BY something
)
```

think:

```text
Complete result
      ↓
PARTITION BY
      ↓
Separate logical windows
      ↓
ORDER BY
      ↓
Arrange rows inside each window
      ↓
Window function
      ↓
Calculate/retrieve something
      ↓
Attach result to every applicable row
```

---

# 68. Three Questions to Ask Yourself

Whenever you see a window function, ask:

### Question 1

> Which rows belong together?

Look at:

```sql
PARTITION BY
```

### Question 2

> In what order are those rows considered?

Look at:

```sql
ORDER BY
```

### Question 3

> What am I doing with those rows?

Look at:

```text
ROW_NUMBER()
RANK()
DENSE_RANK()
FIRST_VALUE()
LAST_VALUE()
NTH_VALUE()
LAG()
LEAD()
```

This mental process makes window functions much easier.

---

# 69. Quick Memory Sheet

## ROW_NUMBER()

```sql
ROW_NUMBER() OVER(
    ORDER BY column
)
```

Unique sequential number.

```text
1
2
3
4
```

---

## RANK()

```sql
RANK() OVER(
    ORDER BY column
)
```

Same rank for ties, gaps appear.

```text
1
2
2
4
```

---

## DENSE_RANK()

```sql
DENSE_RANK() OVER(
    ORDER BY column
)
```

Same rank for ties, no gaps.

```text
1
2
2
3
```

---

## FIRST_VALUE()

```sql
FIRST_VALUE(column) OVER(
    ORDER BY column
)
```

Gets the first value according to the window order.

---

## LAST_VALUE()

```sql
LAST_VALUE(column) OVER(
    ORDER BY column
    ROWS BETWEEN
        UNBOUNDED PRECEDING
        AND UNBOUNDED FOLLOWING
)
```

Gets the last value of the complete frame.

---

## NTH_VALUE()

```sql
NTH_VALUE(column, N) OVER(
    ORDER BY column
    ROWS BETWEEN
        UNBOUNDED PRECEDING
        AND UNBOUNDED FOLLOWING
)
```

Gets the N-th value.

---

## LAG()

```sql
LAG(column) OVER(
    ORDER BY column
)
```

Gets the previous row's value.

---

## LEAD()

```sql
LEAD(column) OVER(
    ORDER BY column
)
```

Gets the next row's value.

---

# 70. Final Summary

A window function allows SQL to look across related rows while keeping the original rows.

The basic structure is:

```sql
FUNCTION(...) OVER(...)
```

`PARTITION BY` answers:

> Which rows belong together?

`ORDER BY` answers:

> In what sequence should those rows be considered?

The ranking functions are:

```text
ROW_NUMBER()
RANK()
DENSE_RANK()
```

The value functions are:

```text
FIRST_VALUE()
LAST_VALUE()
NTH_VALUE()
```

The navigation functions are:

```text
LAG()
LEAD()
```

The most important differences are:

```text
ROW_NUMBER
→ Every row gets a unique number.

RANK
→ Ties get the same rank and gaps appear.

DENSE_RANK
→ Ties get the same rank but no gaps appear.

FIRST_VALUE
→ Gets the first value in the window.

LAST_VALUE
→ Gets the last value in the frame.

NTH_VALUE
→ Gets the value at position N.

LAG
→ Looks backward.

LEAD
→ Looks forward.
```

And the most important conceptual difference is:

```text
GROUP BY
→ reduces/collapses rows

WINDOW FUNCTION
→ calculates across rows but keeps the rows
```

Finally, remember the core mental model:

```text
             WINDOW FUNCTION
                    ↓
             ┌──────────────┐
             │   PARTITION  │
             └──────┬───────┘
                    ↓
             Which rows together?
                    ↓
             ┌──────────────┐
             │    ORDER BY  │
             └──────┬───────┘
                    ↓
             What is the sequence?
                    ↓
             ┌──────────────┐
             │   FUNCTION   │
             └──────┬───────┘
                    ↓
          What should we calculate?
                    ↓
          Result attached to rows
```

Once this model is clear, window functions are no longer eight unrelated functions. They are simply **different operations performed over an ordered or partitioned window of rows**.
