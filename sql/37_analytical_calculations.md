# Advanced SQL Window Functions — Analytical Calculations

Window functions are one of the most important concepts in SQL for data analysis.

They allow us to perform calculations across related rows without collapsing those rows into a single result.

In this section, we will learn:

1. Cumulative Sum
2. Cumulative Average
3. Running / Moving Average
4. Percent Change using LAG()
5. Median
6. PERCENTILE_CONT()
7. PERCENTILE_DISC()
8. Difference between CONT and DISC
9. Quartiles
10. Segmentation using NTILE()
11. Outlier Detection
12. Outlier Filtering / Removal
13. Group-wise analytical calculations
14. Important mental models

---

# 1. Window Functions for Analytical Calculations

In data analysis, we often need to answer questions such as:

- What are the total sales accumulated so far?
- What is the average sales from the beginning until today?
- What is the average of the last 3 months?
- How much did sales increase compared with the previous month?
- What is the median weight?
- What are Q1 and Q3?
- Which observations are outliers?
- Which customers belong to the top 25%?
- How can we divide customers into 4 or 10 segments?

Window functions are extremely useful for these types of analytical problems.

---

# 2. Important Window Function Concept

A normal aggregate function such as:

SELECT AVG(Sales)
FROM sales;

produces one result.

For example:

166.67

But when we use:

AVG(Sales) OVER(...)

SQL keeps the original rows and calculates an analytical value for each row.

For example:

SELECT
    Month,
    Sales,
    AVG(Sales) OVER() AS overall_average
FROM sales;

The result could look like:

| Month | Sales | Overall Average |
|---|---:|---:|
| Jan | 100 | 166.67 |
| Feb | 150 | 166.67 |
| Mar | 120 | 166.67 |
| Apr | 200 | 166.67 |
| May | 250 | 166.67 |
| Jun | 180 | 166.67 |

The important difference is:

AVG(Sales)
    ↓
Aggregate function
    ↓
Collapses rows

AVG(Sales) OVER(...)
    ↓
Window function
    ↓
Keeps rows
    ↓
Calculates an analytical value for each row

---

# 3. Understanding OVER()

The OVER() clause defines the window over which the function operates.

Basic structure:

FUNCTION(column) OVER(...)

Examples:

SUM(Sales) OVER(...)

AVG(Sales) OVER(...)

LAG(Sales) OVER(...)

Inside OVER() we commonly use:

- PARTITION BY
- ORDER BY
- ROWS BETWEEN

These determine which rows belong to the window and how they are processed.

---

# 4. PARTITION BY Inside OVER()

PARTITION BY divides the data into separate groups for the window calculation.

Example:

SUM(Sales) OVER(
    PARTITION BY Region
)

This means:

"Calculate the sum separately for each region."

Suppose the data is:

| Region | Sales |
|---|---:|
| East | 100 |
| East | 150 |
| West | 80 |
| West | 120 |

SQL creates two separate windows:

East:
100
150

West:
80
120

Then:

East total = 250

West total = 200

The original rows are still preserved.

---

# 5. ORDER BY Inside OVER()

ORDER BY determines the order in which the rows are considered by the window function.

Example:

SUM(Sales) OVER(
    ORDER BY Month
)

This tells SQL:

"Process the rows according to Month order."

ORDER BY inside OVER() is extremely important for:

- Cumulative calculations
- Running averages
- LAG()
- LEAD()
- Ranking
- Previous/next row comparisons

Think of it as:

ORDER BY
    ↓
Establish row order
    ↓
Window function
    ↓
Perform calculation according to that order

---

# 6. Example Dataset

We will use the following dataset:

| Month | Sales |
|---|---:|
| Jan | 100 |
| Feb | 150 |
| Mar | 120 |
| Apr | 200 |
| May | 250 |
| Jun | 180 |

Table name:

sales

Columns:

Month
Sales

---

# 7. Cumulative Sum

## Definition

A cumulative sum means:

"Add the current value to all previous values."

For our data:

Jan:

100

Feb:

100 + 150 = 250

Mar:

100 + 150 + 120 = 370

Apr:

100 + 150 + 120 + 200 = 570

May:

100 + 150 + 120 + 200 + 250 = 820

Jun:

100 + 150 + 120 + 200 + 250 + 180 = 1000

Result:

| Month | Sales | Cumulative Sales |
|---|---:|---:|
| Jan | 100 | 100 |
| Feb | 150 | 250 |
| Mar | 120 | 370 |
| Apr | 200 | 570 |
| May | 250 | 820 |
| Jun | 180 | 1000 |

---

# 8. Cumulative Sum SQL

SELECT
    Month,
    Sales,
    SUM(Sales) OVER(
        ORDER BY Month
    ) AS cumulative_sales
FROM sales;

The important part is:

SUM(Sales) OVER(
    ORDER BY Month
)

Because the rows are ordered by Month, SQL progressively adds the values.

---

# 9. Explicit Window Frame for Cumulative Sum

We can explicitly tell SQL which rows belong to the window.

SELECT
    Month,
    Sales,
    SUM(Sales) OVER(
        ORDER BY Month
        ROWS BETWEEN UNBOUNDED PRECEDING
        AND CURRENT ROW
    ) AS cumulative_sales
FROM sales;

Meaning:

UNBOUNDED PRECEDING
    ↓
Start from the first row

CURRENT ROW
    ↓
Stop at the current row

For April, the window contains:

Jan
Feb
Mar
Apr ← current row

So SQL calculates:

100 + 150 + 120 + 200

= 570

---

# 10. Understanding ROWS BETWEEN

The general syntax is:

ROWS BETWEEN start AND end

For example:

ROWS BETWEEN UNBOUNDED PRECEDING
AND CURRENT ROW

means:

First row → Current row

Another example:

ROWS BETWEEN 2 PRECEDING
AND CURRENT ROW

means:

Two previous rows + current row

Another example:

ROWS BETWEEN UNBOUNDED PRECEDING
AND UNBOUNDED FOLLOWING

means:

All rows in the window.

---

# 11. Important Window Frames

## Entire Window

ROWS BETWEEN UNBOUNDED PRECEDING
AND UNBOUNDED FOLLOWING

Meaning:

All rows.

## Cumulative Window

ROWS BETWEEN UNBOUNDED PRECEDING
AND CURRENT ROW

Meaning:

First row → Current row.

## Moving Three-Row Window

ROWS BETWEEN 2 PRECEDING
AND CURRENT ROW

Meaning:

Two previous rows + current row.

---

# 12. Cumulative Sum With PARTITION BY

Suppose we have:

| Region | Month | Sales |
|---|---|---:|
| East | Jan | 100 |
| East | Feb | 150 |
| East | Mar | 200 |
| West | Jan | 80 |
| West | Feb | 120 |
| West | Mar | 100 |

We want cumulative sales separately for each region.

SELECT
    Region,
    Month,
    Sales,
    SUM(Sales) OVER(
        PARTITION BY Region
        ORDER BY Month
    ) AS cumulative_sales
FROM sales;

Now SQL creates separate windows:

East:
Jan → Feb → Mar

West:
Jan → Feb → Mar

The calculation does not mix East and West.

---

# 13. Cumulative Average

## Definition

A cumulative average means:

"Calculate the average from the first row up to the current row."

For:

100
150
120
200
250
180

we calculate:

January:

100 / 1 = 100

February:

(100 + 150) / 2 = 125

March:

(100 + 150 + 120) / 3 = 123.33

April:

(100 + 150 + 120 + 200) / 4 = 142.50

The same process continues for the remaining rows.

---

# 14. Cumulative Average SQL

SELECT
    Month,
    Sales,
    AVG(Sales) OVER(
        ORDER BY Month
    ) AS cumulative_average
FROM sales;

Explicit version:

SELECT
    Month,
    Sales,
    AVG(Sales) OVER(
        ORDER BY Month
        ROWS BETWEEN UNBOUNDED PRECEDING
        AND CURRENT ROW
    ) AS cumulative_average
FROM sales;

---

# 15. Cumulative Average vs Overall Average

These are different concepts.

## Overall Average

SELECT AVG(Sales)
FROM sales;

This gives one average for the entire dataset.

## Window Overall Average

SELECT
    Month,
    Sales,
    AVG(Sales) OVER() AS overall_average
FROM sales;

Every row receives the same overall average.

## Cumulative Average

SELECT
    Month,
    Sales,
    AVG(Sales) OVER(
        ORDER BY Month
    ) AS cumulative_average
FROM sales;

The average changes as we move through the rows.

---

# 16. Running Average

A running average is commonly used to mean a moving average over a fixed number of recent rows.

For example:

"Calculate the average of the current row and the previous 2 rows."

This creates a 3-row moving average.

---

# 17. Three-Row Running Average

Our data:

Jan = 100
Feb = 150
Mar = 120
Apr = 200
May = 250
Jun = 180

January:

Only one row is available.

100

Average:

100

February:

100 + 150
-----------
    2

= 125

March:

100 + 150 + 120
---------------
       3

= 123.33

April:

150 + 120 + 200
---------------
       3

= 156.67

May:

120 + 200 + 250
---------------
       3

= 190

June:

200 + 250 + 180
---------------
       3

= 210

---

# 18. Running Average SQL

SELECT
    Month,
    Sales,
    AVG(Sales) OVER(
        ORDER BY Month
        ROWS BETWEEN 2 PRECEDING
        AND CURRENT ROW
    ) AS running_average
FROM sales;

The key part is:

ROWS BETWEEN 2 PRECEDING
AND CURRENT ROW

This means:

Current row
+
2 previous rows

---

# 19. Cumulative Average vs Running Average

This distinction is extremely important.

## Cumulative Average

AVG(Sales) OVER(
    ORDER BY Month
    ROWS BETWEEN UNBOUNDED PRECEDING
    AND CURRENT ROW
)

Window:

Jan

Jan → Feb

Jan → Feb → Mar

Jan → Feb → Mar → Apr

Jan → Feb → Mar → Apr → May

The window continuously grows.

## Three-Row Running Average

AVG(Sales) OVER(
    ORDER BY Month
    ROWS BETWEEN 2 PRECEDING
    AND CURRENT ROW
)

Window:

Jan

Jan → Feb

Jan → Feb → Mar

Feb → Mar → Apr

Mar → Apr → May

Apr → May → Jun

The window moves.

---

# 20. LAG()

LAG() allows us to access a value from a previous row.

Basic syntax:

LAG(column) OVER(
    ORDER BY column
)

Example:

SELECT
    Month,
    Sales,
    LAG(Sales) OVER(
        ORDER BY Month
    ) AS previous_sales
FROM sales;

Result:

| Month | Sales | Previous Sales |
|---|---:|---:|
| Jan | 100 | NULL |
| Feb | 150 | 100 |
| Mar | 120 | 150 |
| Apr | 200 | 120 |
| May | 250 | 200 |
| Jun | 180 | 250 |

January is NULL because there is no previous row.

---

# 21. LAG() With an Offset

By default:

LAG(Sales)

means:

1 row before.

But we can specify an offset:

LAG(Sales, 2)

means:

2 rows before.

Example:

SELECT
    Month,
    Sales,
    LAG(Sales, 2) OVER(
        ORDER BY Month
    ) AS sales_two_months_ago
FROM sales;

---

# 22. LEAD()

LEAD() is the opposite of LAG().

LAG():

Previous row

LEAD():

Next row

Example:

SELECT
    Month,
    Sales,
    LEAD(Sales) OVER(
        ORDER BY Month
    ) AS next_sales
FROM sales;

Conceptually:

| Month | Sales | Next Sales |
|---|---:|---:|
| Jan | 100 | 150 |
| Feb | 150 | 120 |
| Mar | 120 | 200 |
| Apr | 200 | 250 |
| May | 250 | 180 |
| Jun | 180 | NULL |

---

# 23. Percent Change

Percent change tells us how much a value increased or decreased compared with a previous value.

Formula:

Percent Change =
(New Value - Old Value)
----------------------- × 100
       Old Value

Or:

(New - Old) / Old × 100

---

# 24. Simple Percent Change Example

Suppose:

Old = 100
New = 150

Then:

(150 - 100) / 100 × 100

= 50%

Therefore:

+50%

means the value increased by 50%.

---

# 25. Negative Percent Change

Suppose:

Old = 150
New = 120

Then:

(120 - 150) / 150 × 100

= -20%

The negative value means:

20% decrease.

Therefore:

Positive percentage
    ↓
Increase

Negative percentage
    ↓
Decrease

---

# 26. Finding the Old Value Using LAG()

The previous value is the old value.

LAG(Sales) OVER(
    ORDER BY Month
)

Conceptually:

Current Sales
    ↓
New value

LAG(Sales)
    ↓
Old value

---

# 27. Percent Change Using LAG()

SELECT
    Month,
    Sales,
    LAG(Sales) OVER(
        ORDER BY Month
    ) AS old_sales,

    (
        (
            Sales - LAG(Sales) OVER(
                ORDER BY Month
            )
        )
        /
        LAG(Sales) OVER(
            ORDER BY Month
        )
    ) * 100 AS percent_change

FROM sales;

The calculation is:

Current Sales
    ↓
New value

LAG(Sales)
    ↓
Old value

(New - Old) / Old × 100
    ↓
Percent Change

---

# 28. Handling Division by Zero

Suppose the previous value is:

0

Then:

(New - 0) / 0

causes a division-by-zero problem.

A safer method is:

NULLIF(value, 0)

For example:

SELECT
    Month,
    Sales,

    (
        (
            Sales - LAG(Sales) OVER(
                ORDER BY Month
            )
        )
        /
        NULLIF(
            LAG(Sales) OVER(
                ORDER BY Month
            ),
            0
        )
    ) * 100 AS percent_change

FROM sales;

NULLIF(value, 0) returns NULL when the value is zero.

---

# 29. Better Percent Change Using a CTE

When the previous value is required multiple times, a CTE makes the query cleaner.

WITH previous_sales AS (
    SELECT
        Month,
        Sales,
        LAG(Sales) OVER(
            ORDER BY Month
        ) AS old_sales
    FROM sales
)

SELECT
    Month,
    Sales,
    old_sales,
    (
        (Sales - old_sales)
        /
        NULLIF(old_sales, 0)
    ) * 100 AS percent_change

FROM previous_sales;

The logic is:

Step 1:
Find previous value.

Step 2:
Store it as old_sales.

Step 3:
Calculate percentage change.

---

# 30. Percent Change Within Groups

Suppose:

| Region | Month | Sales |
|---|---|---:|
| East | Jan | 100 |
| East | Feb | 150 |
| East | Mar | 200 |
| West | Jan | 80 |
| West | Feb | 120 |
| West | Mar | 100 |

We want the previous sales separately for each region.

Use:

LAG(Sales) OVER(
    PARTITION BY Region
    ORDER BY Month
)

Complete query:

WITH previous_sales AS (
    SELECT
        Region,
        Month,
        Sales,
        LAG(Sales) OVER(
            PARTITION BY Region
            ORDER BY Month
        ) AS old_sales
    FROM sales
)

SELECT
    Region,
    Month,
    Sales,
    old_sales,
    (
        (Sales - old_sales)
        /
        NULLIF(old_sales, 0)
    ) * 100 AS percent_change

FROM previous_sales;

The first month of every region gets NULL because there is no previous month within that region.

---

# 31. Median

Median is the middle value after sorting the data.

Example:

10
20
30
40
50

The middle value is:

30

Therefore:

Median = 30

For an even number of values:

10
20
30
40

The middle values are:

20
30

Median:

(20 + 30) / 2

= 25

---

# 32. Median Using PERCENTILE_CONT()

The median is the 50th percentile.

Therefore:

Median = 50th percentile = 0.5

Example:

SELECT
    PERCENTILE_CONT(0.5)
    WITHIN GROUP(
        ORDER BY Weight
    ) AS median_weight
FROM athlete;

---

# 33. What Is PERCENTILE_CONT()?

PERCENTILE_CONT() means:

PERCENTILE
+
CONTINUOUS

It treats the percentile as a continuous position.

It can interpolate between values.

This means its result does not necessarily have to exist in the original dataset.

---

# 34. PERCENTILE_CONT() Example

Suppose:

10
20
30
40

The median lies between:

20 and 30

Continuous calculation:

(20 + 30) / 2

= 25

Therefore:

PERCENTILE_CONT(0.5)

can return:

25

Notice that 25 does not exist in the dataset.

That is acceptable because the continuous percentile can interpolate between values.

---

# 35. PERCENTILE_DISC()

PERCENTILE_DISC() means:

PERCENTILE
+
DISCRETE

It returns a value from the actual ordered dataset.

It does not create an interpolated value like 25.

Example:

SELECT
    PERCENTILE_DISC(0.5)
    WITHIN GROUP(
        ORDER BY Weight
    ) AS median_weight
FROM athlete;

The exact discrete percentile selection follows the database's percentile definition, but the key concept is:

PERCENTILE_DISC()
    ↓
Chooses an existing value.

---

# 36. PERCENTILE_CONT() vs PERCENTILE_DISC()

This is extremely important.

| Function | Meaning | Can interpolate? | Result must exist in dataset? |
|---|---|---|---|
| PERCENTILE_CONT() | Continuous percentile | Yes | No |
| PERCENTILE_DISC() | Discrete percentile | No | Yes |

Easy way to remember:

CONT
    ↓
Continuous
    ↓
Can calculate between values.

DISC
    ↓
Discrete
    ↓
Selects an existing value.

---

# 37. Example of CONT vs DISC

Dataset:

10
20
30
40

At the median:

20 ← middle → 30

PERCENTILE_CONT() can calculate:

25

because:

(20 + 30) / 2 = 25

But PERCENTILE_DISC() must return an actual value from the dataset according to its discrete percentile rule.

The important idea is:

PERCENTILE_CONT()
    ↓
Interpolated result possible.

PERCENTILE_DISC()
    ↓
Actual dataset value.

---

# 38. Quartiles

Quartiles divide ordered data into four parts.

There are three important quartiles:

Q1 = 25th percentile
Q2 = 50th percentile
Q3 = 75th percentile

Where:

Q1
    ↓
25% of observations are below it.

Q2
    ↓
50% of observations are below it.
    ↓
Median.

Q3
    ↓
75% of observations are below it.

---

# 39. Finding Q1, Q2 and Q3

Using PERCENTILE_CONT():

SELECT
    PERCENTILE_CONT(0.25)
        WITHIN GROUP(
            ORDER BY Weight
        ) AS Q1,

    PERCENTILE_CONT(0.50)
        WITHIN GROUP(
            ORDER BY Weight
        ) AS Median,

    PERCENTILE_CONT(0.75)
        WITHIN GROUP(
            ORDER BY Weight
        ) AS Q3

FROM athlete;

---

# 40. Percentiles by Group

Suppose we want median weight for every sport.

SELECT
    Sport,

    PERCENTILE_CONT(0.5)
        WITHIN GROUP(
            ORDER BY Weight
        ) AS median_weight

FROM athlete
GROUP BY Sport;

The idea is:

Swimming
    ↓
Calculate swimmer median.

Boxing
    ↓
Calculate boxer median.

Running
    ↓
Calculate runner median.

Each group gets its own percentile calculation.

---

# 41. Segmentation

Segmentation means dividing data into groups according to an ordering or business rule.

For example, customers can be divided into:

Top 25%
Upper-middle 25%
Lower-middle 25%
Bottom 25%

This is useful for:

- Customer segmentation
- Sales analysis
- Ranking customers
- Performance analysis
- Marketing
- Risk analysis

---

# 42. NTILE()

NTILE() divides ordered rows into approximately equal-sized buckets.

Basic syntax:

NTILE(number_of_buckets) OVER(
    ORDER BY column
)

Example:

SELECT
    Customer,
    Sales,

    NTILE(4) OVER(
        ORDER BY Sales
    ) AS segment

FROM customers;

---

# 43. Understanding NTILE(4)

NTILE(4) means:

Divide the rows into 4 buckets.

Conceptually:

Bucket 1
    ↓
Lowest values.

Bucket 2
    ↓
Lower-middle values.

Bucket 3
    ↓
Upper-middle values.

Bucket 4
    ↓
Highest values.

This happens because:

ORDER BY Sales

uses ascending order by default.

---

# 44. NTILE() With DESC

If we use:

NTILE(4) OVER(
    ORDER BY Sales DESC
)

then:

Bucket 1
    ↓
Highest sales.

Bucket 2
    ↓
High sales.

Bucket 3
    ↓
Low sales.

Bucket 4
    ↓
Lowest sales.

Therefore always pay attention to:

ASC
    ↓
Ascending.

DESC
    ↓
Descending.

---

# 45. Different Numbers of Buckets

Examples:

NTILE(2)

creates:

2 groups.

NTILE(4)

creates:

4 groups.

NTILE(10)

creates:

10 groups.

NTILE(10) is useful for decile-style segmentation.

---

# 46. NTILE() With PARTITION BY

Suppose customers belong to different countries.

We want four sales segments separately for each country.

SELECT
    Country,
    Customer,
    Sales,

    NTILE(4) OVER(
        PARTITION BY Country
        ORDER BY Sales DESC
    ) AS sales_segment

FROM customers;

Now SQL creates separate segmentation systems:

Pakistan
    ↓
4 segments.

India
    ↓
4 segments.

USA
    ↓
4 segments.

---

# 47. NTILE() Is Not Exactly a Percentile

These concepts are related but different.

NTILE()
    ↓
Assigns rows to buckets.

PERCENT_RANK()
    ↓
Calculates relative rank position.

CUME_DIST()
    ↓
Calculates cumulative distribution.

Therefore:

NTILE
    ↓
Bucket assignment.

PERCENT_RANK
    ↓
Relative rank.

CUME_DIST
    ↓
Cumulative proportion.

---

# 48. Outlier Detection

An outlier is a value that is unusually far from the rest of the data.

Example:

50
52
51
53
55
54
200

The value:

200

looks unusually high.

But remember:

An outlier is not automatically an incorrect value.

It may represent a genuine extreme observation.

---

# 49. IQR Method for Outlier Detection

One of the most common statistical approaches is the IQR method.

IQR stands for:

Interquartile Range.

Formula:

IQR = Q3 - Q1

Where:

Q1 = 25th percentile.

Q3 = 75th percentile.

---

# 50. Outlier Boundaries

Once we have Q1 and Q3:

IQR = Q3 - Q1

Lower boundary:

Lower Boundary =
Q1 - 1.5 × IQR

Upper boundary:

Upper Boundary =
Q3 + 1.5 × IQR

Then:

Value < Lower Boundary
    ↓
Outlier.

Value > Upper Boundary
    ↓
Outlier.

Values between the two boundaries are considered non-outliers under this rule.

---

# 51. IQR Example

Suppose:

Q1 = 50
Q3 = 70

Then:

IQR = Q3 - Q1
    = 70 - 50
    = 20

Lower boundary:

50 - 1.5 × 20
= 50 - 30
= 20

Upper boundary:

70 + 1.5 × 20
= 70 + 30
= 100

Therefore:

Below 20
    ↓
Outlier.

20 to 100
    ↓
Normal range.

Above 100
    ↓
Outlier.

---

# 52. Finding Q1 and Q3 in SQL

SELECT
    PERCENTILE_CONT(0.25)
        WITHIN GROUP(
            ORDER BY Weight
        ) AS q1,

    PERCENTILE_CONT(0.75)
        WITHIN GROUP(
            ORDER BY Weight
        ) AS q3

FROM athlete;

This gives us the two values required to calculate IQR.

---

# 53. Calculate IQR and Boundaries

A CTE makes the calculation easier to understand.

WITH quartiles AS (
    SELECT
        PERCENTILE_CONT(0.25)
            WITHIN GROUP(
                ORDER BY Weight
            ) AS q1,

        PERCENTILE_CONT(0.75)
            WITHIN GROUP(
                ORDER BY Weight
            ) AS q3

    FROM athlete
)

SELECT
    q1,
    q3,
    q3 - q1 AS iqr,

    q1 - 1.5 * (q3 - q1) AS lower_bound,

    q3 + 1.5 * (q3 - q1) AS upper_bound

FROM quartiles;

The process is:

Q1 + Q3
    ↓
Calculate IQR.
    ↓
Calculate lower boundary.
    ↓
Calculate upper boundary.

---

# 54. Classifying Rows as Normal or Outlier

We can use CASE to classify each row.

WITH quartiles AS (
    SELECT
        PERCENTILE_CONT(0.25)
            WITHIN GROUP(
                ORDER BY Weight
            ) AS q1,

        PERCENTILE_CONT(0.75)
            WITHIN GROUP(
                ORDER BY Weight
            ) AS q3

    FROM athlete
)

SELECT
    a.*,

    CASE
        WHEN a.Weight < q.q1 - 1.5 * (q.q3 - q.q1)
          OR a.Weight > q.q3 + 1.5 * (q.q3 - q.q1)
        THEN 'Outlier'

        ELSE 'Normal'
    END AS status

FROM athlete a
CROSS JOIN quartiles q;

The logic is:

Calculate Q1 and Q3
        ↓
Calculate IQR
        ↓
Calculate boundaries
        ↓
Check every row
        ↓
Normal or Outlier

---

# 55. Filtering Outliers

Usually, we should not physically delete the original data just because it is an outlier.

Instead, we can filter the outliers from a particular analysis.

WITH quartiles AS (
    SELECT
        PERCENTILE_CONT(0.25)
            WITHIN GROUP(
                ORDER BY Weight
            ) AS q1,

        PERCENTILE_CONT(0.75)
            WITHIN GROUP(
                ORDER BY Weight
            ) AS q3

    FROM athlete
)

SELECT
    a.*

FROM athlete a
CROSS JOIN quartiles q

WHERE a.Weight >= q.q1 - 1.5 * (q.q3 - q.q1)
  AND a.Weight <= q.q3 + 1.5 * (q.q3 - q.q1);

This returns the non-outlier observations.

It does not delete the original records.

---

# 56. Detection vs Filtering vs Deletion

These three concepts are different.

## Detection

Determine whether a value is an outlier.

## Filtering

Exclude outliers from a particular analysis.

For example:

WHERE Weight >= lower_bound
AND Weight <= upper_bound

## Physical Deletion

DELETE FROM athlete
WHERE ...;

This actually changes the database.

For analytical work, filtering is generally safer because the original data remains available.

---

# 57. Outlier Detection by Group

Sometimes a global outlier calculation is inappropriate.

For example, athlete weights from different sports may have very different distributions.

Instead of calculating one global Q1 and Q3, we may want:

Swimming
    ↓
Q1 and Q3 for swimmers.

Boxing
    ↓
Q1 and Q3 for boxers.

Gymnastics
    ↓
Q1 and Q3 for gymnasts.

One way to calculate group-level quartiles is:

SELECT
    Sport,

    PERCENTILE_CONT(0.25)
        WITHIN GROUP(
            ORDER BY Weight
        ) AS q1,

    PERCENTILE_CONT(0.75)
        WITHIN GROUP(
            ORDER BY Weight
        ) AS q3

FROM athlete
GROUP BY Sport;

These group-specific boundaries can then be joined back to the original rows to classify outliers within each sport.

---

# 58. Important Mental Model for OVER()

Whenever you see:

FUNCTION() OVER(...)

think:

"Calculate this function across a window while keeping the original rows."

Then inspect the contents of OVER().

---

# 59. Mental Model of PARTITION BY

When you see:

OVER(
    PARTITION BY Sport
)

Think:

Divide the data
    ↓
Into separate groups
    ↓
Perform the window calculation
    ↓
Separately for each group.

---

# 60. Mental Model of ORDER BY

When you see:

OVER(
    ORDER BY Date
)

Think:

Arrange rows
    ↓
According to Date
    ↓
Perform calculation in that order.

This is especially important for:

- LAG()
- LEAD()
- Running Average
- Cumulative Sum
- Cumulative Average
- Ranking

---

# 61. Mental Model of Window Frames

When you see:

ROWS BETWEEN ...

Think:

"Define exactly which rows are included in the calculation."

Example:

ROWS BETWEEN UNBOUNDED PRECEDING
AND CURRENT ROW

means:

First row → Current row.

Example:

ROWS BETWEEN 2 PRECEDING
AND CURRENT ROW

means:

Two previous rows + Current row.

Example:

ROWS BETWEEN UNBOUNDED PRECEDING
AND UNBOUNDED FOLLOWING

means:

All rows.

---

# 62. Complete Analytical Picture

                    ANALYTICAL SQL
                          |
        +-----------------+------------------+
        |                 |                  |
   CUMULATIVE           CHANGE          DISTRIBUTION
        |                 |                  |
    SUM / AVG           LAG()          PERCENTILE
        |                 |                  |
   First → Current       |          +-------+-------+
        |                 |          |               |
        |           Percent Change   CONT            DISC
        |                 |          |               |
        |          (New-Old)/Old     |               |
        |                             |               |
        +-----------------------------+---------------+
                                      |
                                SEGMENTATION
                                      |
                                    NTILE
                                      |
                              +-------+-------+
                              |               |
                           NTILE(4)       NTILE(10)
                              |               |
                           Quartiles        Deciles
                                     
                                OUTLIER ANALYSIS
                                      |
                                  Q1 / Q3
                                      |
                                IQR = Q3-Q1
                                      |
                          +-----------+-----------+
                          |                       |
                    Lower Boundary         Upper Boundary
                    Q1 - 1.5×IQR           Q3 + 1.5×IQR
                          |                       |
                          +-----------+-----------+
                                      |
                              Normal / Outlier
                                      |
                                  Filtering

---

# 63. Most Important SQL Patterns

## Cumulative Sum

SUM(column) OVER(
    ORDER BY column
    ROWS BETWEEN UNBOUNDED PRECEDING
    AND CURRENT ROW
)

## Cumulative Average

AVG(column) OVER(
    ORDER BY column
    ROWS BETWEEN UNBOUNDED PRECEDING
    AND CURRENT ROW
)

## Three-Row Running Average

AVG(column) OVER(
    ORDER BY column
    ROWS BETWEEN 2 PRECEDING
    AND CURRENT ROW
)

## Previous Value

LAG(column) OVER(
    ORDER BY column
)

## Next Value

LEAD(column) OVER(
    ORDER BY column
)

## Previous Value With Offset

LAG(column, 2) OVER(
    ORDER BY column
)

This means:

2 rows before.

## Percent Change

(New - Old) / Old × 100

where:

Old = LAG(column)

## Median

PERCENTILE_CONT(0.5)
WITHIN GROUP(
    ORDER BY column
)

## Q1

PERCENTILE_CONT(0.25)
WITHIN GROUP(
    ORDER BY column
)

## Q3

PERCENTILE_CONT(0.75)
WITHIN GROUP(
    ORDER BY column
)

## IQR

IQR = Q3 - Q1

## Lower Outlier Boundary

Q1 - 1.5 × IQR

## Upper Outlier Boundary

Q3 + 1.5 × IQR

## Segmentation

NTILE(4) OVER(
    ORDER BY column
)

---

# 64. Final Differences You Must Remember

## Cumulative Average vs Running Average

Cumulative Average:

First row → Current row

The window keeps growing.

Running / Moving Average:

A fixed number of recent rows.

The window moves.

---

# 65. LAG() vs LEAD()

LAG()
    ↓
Previous row.

LEAD()
    ↓
Next row.

---

# 66. PERCENTILE_CONT() vs PERCENTILE_DISC()

PERCENTILE_CONT()
    ↓
Continuous.
    ↓
Can interpolate.
    ↓
Result may not exist in dataset.

PERCENTILE_DISC()
    ↓
Discrete.
    ↓
Does not interpolate.
    ↓
Returns an existing ordered value.

---

# 67. NTILE() vs Percentile

NTILE()
    ↓
Assign rows to buckets.

Percentile:
    ↓
Determine a position/value in a distribution.

They are related but they do different jobs.

---

# 68. Cumulative Sum vs Cumulative Average

Cumulative Sum:

SUM(column) OVER(
    ORDER BY ...
)

Adds values progressively.

Cumulative Average:

AVG(column) OVER(
    ORDER BY ...
)

Calculates the average progressively.

Example:

Values:

100
200
300

Cumulative Sum:

100
300
600

Cumulative Average:

100
150
200

---

# 69. Cumulative vs Running Window

Cumulative:

ROWS BETWEEN UNBOUNDED PRECEDING
AND CURRENT ROW

Meaning:

Start from the first row and continue to the current row.

Running / Moving:

ROWS BETWEEN N PRECEDING
AND CURRENT ROW

Meaning:

Only use a fixed number of previous rows plus the current row.

---

# 70. Why LAG() Is So Important

LAG() is extremely useful for time-series analysis.

For example:

Current Sales = 150

Previous Sales = 100

Then:

Difference:

150 - 100 = 50

Percentage change:

(150 - 100) / 100 × 100

= 50%

Therefore:

LAG()
    ↓
Previous value
    ↓
Difference
    ↓
Percent change
    ↓
Growth analysis

---

# 71. Why Median Is Important

Average can sometimes be heavily influenced by extreme values.

Example:

10
10
10
10
1000

Mean:

(10 + 10 + 10 + 10 + 1000) / 5

= 208

But the median is:

10

Therefore, median can sometimes give a better representation of the center when the data is skewed or contains extreme values.

---

# 72. Why IQR Is Useful for Outliers

The IQR method focuses on the middle 50% of the data.

Q1:

25th percentile.

Q3:

75th percentile.

Therefore:

Q3 - Q1

represents the spread of the middle 50%.

The 1.5 × IQR rule then identifies values unusually far below Q1 or above Q3.

---

# 73. Important Warning About Outliers

Do not automatically delete every outlier.

An outlier can be:

1. A data entry error.
2. A measurement error.
3. A genuine extreme observation.
4. A rare but valid event.

Therefore, the correct workflow is usually:

Detect
    ↓
Investigate
    ↓
Understand
    ↓
Decide whether to keep, transform, or exclude.

Do not blindly delete.

---

# 74. Final Learning Checklist

Before moving forward, make sure you can explain these without simply memorizing the query:

- [ ] What is a window function?
- [ ] Why do we use OVER()?
- [ ] What does PARTITION BY do inside OVER()?
- [ ] Why is ORDER BY important inside OVER()?
- [ ] What does ROWS BETWEEN mean?
- [ ] What is a cumulative sum?
- [ ] What is a cumulative average?
- [ ] What is a running/moving average?
- [ ] Difference between cumulative and running average.
- [ ] What does LAG() do?
- [ ] What does LEAD() do?
- [ ] What is an offset in LAG() or LEAD()?
- [ ] How do we calculate percent change?
- [ ] Why do we use LAG() for percent change?
- [ ] Why can division by zero occur?
- [ ] How does NULLIF() help?
- [ ] What is a median?
- [ ] What is a percentile?
- [ ] What does PERCENTILE_CONT() do?
- [ ] What does PERCENTILE_DISC() do?
- [ ] Difference between CONT and DISC.
- [ ] What are Q1, Q2 and Q3?
- [ ] What is IQR?
- [ ] How are outlier boundaries calculated?
- [ ] How do we classify outliers?
- [ ] Why is filtering usually safer than deleting?
- [ ] What is NTILE()?
- [ ] How does NTILE(4) work?
- [ ] Difference between ASC and DESC in NTILE().
- [ ] How does PARTITION BY work with NTILE()?
- [ ] Difference between segmentation and percentile analysis.

---

# 75. The Most Important Mental Model

Do not try to memorize window functions as isolated functions.

Instead, think about them using this structure:

                WINDOW FUNCTION
                       |
                       ↓
              FUNCTION(column)
                       |
                       ↓
                    OVER()
                       |
             +---------+---------+
             |                   |
             ↓                   ↓
       PARTITION BY           ORDER BY
             |                   |
             ↓                   ↓
      Separate groups       Establish order
                                 |
                                 ↓
                              ROWS
                                 |
                                 ↓
                         Define the window

For example:

AVG(Sales) OVER(
    PARTITION BY Region
    ORDER BY Month
    ROWS BETWEEN 2 PRECEDING
    AND CURRENT ROW
)

Read it in plain English as:

"For each region, order the rows by month, take the current row and the previous two rows, and calculate their average."

This is the correct way to understand window functions.

Do not simply memorize:

AVG(...)
SUM(...)
LAG(...)
LEAD(...)
NTILE(...)

Instead, understand:

FUNCTION
    ↓
OVER()
    ↓
PARTITION BY
    ↓
ORDER BY
    ↓
Window Frame
    ↓
Calculation

---

# 76. Final Summary

The major analytical techniques covered in this section are:

Cumulative Sum
    ↓
SUM() OVER(ORDER BY ...)

Cumulative Average
    ↓
AVG() OVER(ORDER BY ...)

Running Average
    ↓
AVG() OVER(
    ORDER BY ...
    ROWS BETWEEN ...
)

Previous Value
    ↓
LAG()

Next Value
    ↓
LEAD()

Percent Change
    ↓
(New - Old) / Old × 100

Median
    ↓
PERCENTILE_CONT(0.5)

Quartiles
    ↓
Q1 = 0.25
Q2 = 0.50
Q3 = 0.75

Segmentation
    ↓
NTILE()

Outlier Detection
    ↓
Q1 / Q3
    ↓
IQR
    ↓
Q1 - 1.5 × IQR
Q3 + 1.5 × IQR

Filtering
    ↓
Exclude outliers from analysis without deleting the original data.

These techniques form an important part of analytical SQL and are especially useful when working with real-world data analysis problems.