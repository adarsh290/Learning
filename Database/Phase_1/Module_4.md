`Checkpoint 4.1`: JOINs Conceptually

1. Analogy: Piecing Together Information

Imagine you have two separate lists:

- One list has Student IDs and Student Names.
- Another list has Student IDs and Course Names (which courses each student is enrolled in).

If you want to know "Which course is Alice taking?", you can't get that from just the "Student Names" list. You need to
look at Alice's Student ID on the first list, then find that same Student ID on the second list to see her courses.

JOINs in SQL do exactly this: they help you intelligently piece together related information that's stored in different
places.

2. Concept: Combining Data from Multiple Tables

So far, all our queries have focused on a single table. But in relational databases, information is often split across many
tables to avoid redundancy (repeating the same data over and over) and maintain data integrity.

- The Problem: How do you get a complete picture if the employee's name is in one table and their department's full name
  is in another?
- The Solution: The JOIN clause in SQL allows you to combine rows from two or more tables. It does this by finding related
  columns between them. These related columns are almost always a Primary Key in one table and a Foreign Key in another.
- How it Works (Conceptually): When you JOIN two tables, the database essentially creates a temporary, combined view of
  the data. It looks for matching values in the columns you specify, and whenever it finds a match, it takes the row from
  the first table and the corresponding row(s) from the second table and "stitches" them together into a single, wider row
  in the result set.
- Types of JOINs: There are different kinds of JOINs (INNER, LEFT, RIGHT, FULL OUTER), which we'll explore in detail soon.
  For now, just understand that they are all about bringing data together.

3. Example

Let's use a very common scenario: departments and employees.

Table: `departments`

┌─────────┬───────────────┐
│ dept_id │ dept_name │
├─────────┼───────────────┤
│ 101 │ 'Engineering' │
│ 102 │ 'Sales' │
│ 103 │ 'HR' │
└─────────┴───────────────┘

Table: `employees`

┌────────┬───────────┬─────────┐
│ emp_id │ emp_name │ dept_id │
├────────┼───────────┼─────────┤
│ 1 │ 'Alice' │ 101 │
│ 2 │ 'Bob' │ 101 │
│ 3 │ 'Charlie' │ 102 │
│ 4 │ 'Diana' │ 103 │
└────────┴───────────┴─────────┘

| 5 | 'Eve' | 104 | -- (Note: dept_id 104 does not exist in 'departments' table)

If we want to see each employee's name and their department's name, we need to JOIN these two tables. We'd link them using
the dept_id column, as it appears in both tables.

When we JOIN them, SQL looks for rows where employees.dept_id matches departments.dept_id.

Conceptual Combined Result (focus on matches for now):

┌────────┬───────────┬───────────────────┬─────────────────────┬───────────────┐
│ emp_id │ emp_name │ employees.dept_id │ departments.dept_id │ dept_name │
├────────┼───────────┼───────────────────┼─────────────────────┼───────────────┤
│ 1 │ 'Alice' │ 101 │ 101 │ 'Engineering' │
│ 2 │ 'Bob' │ 101 │ 101 │ 'Engineering' │
│ 3 │ 'Charlie' │ 102 │ 102 │ 'Sales' │
│ 4 │ 'Diana' │ 103 │ 103 │ 'HR' │
└────────┴───────────┴───────────────────┴─────────────────────┴───────────────┘

Notice how 'Eve' is not in this conceptual result, because her dept_id (104) doesn't have a match in the departments table.
This behavior is key to understanding JOINs, and we'll see why when we look at INNER JOIN next.

4. Summary

JOINs are fundamental for working with relational databases. They allow you to bring together data from multiple tables
into a single result set by matching values in related columns (typically primary and foreign keys). This enables you to
get a complete view of your interconnected data.

`Checkpoint 4.2: `INNER JOIN

1. Analogy: "Only the Common Ground"

If JOINing tables is like combining information from different lists, an INNER JOIN is like finding only the items that
appear on both lists.

Imagine you have:

- List A: All students (Student ID, Name)
- List B: All enrolled courses (Student ID, Course Name)

An INNER JOIN would only show you students who are also enrolled in a course, and courses that are also taken by a student.
If a student isn't enrolled in any course, or if a course has no students, they simply won't appear in the combined result.
It's the intersection of a Venn diagram.

2. Concept: Matching Rows from Both Tables

The INNER JOIN (or often just JOIN, as INNER is the default in many SQL databases) combines rows from two tables to create
a new result set. It works like this:

- It compares the values in the specified matching columns (defined in the ON clause) from both tables.
- It includes a row in the result set only if there is a match in both tables.
- Rows from either table that do not have a corresponding match in the other table are excluded from the final result.

Syntax:
1 SELECT columns_you_want
2 FROM table1
3 INNER JOIN table2 ON table1.matching_column = table2.matching_column;

- `SELECT columns_you_want`: You select columns from either table1, table2, or both. It's good practice to prefix column
  names with their table name (e.g., table1.column_name) to avoid ambiguity, especially if column names are the same in
  both tables.
- `FROM table1`: Specifies the primary table you're starting from.
- `INNER JOIN table2`: Specifies the second table you're joining with.
- `ON table1.matching_column = table2.matching_column`: This is your JOIN condition. It tells SQL how to match rows
  between table1 and table2. These matching_columns are typically the primary key of one table and the foreign key of the
  other.

3. Example

Let's use our departments and employees tables again. We'll add a 'Marketing' department that has no employees and an 'Eve'
employee who has an invalid dept_id.

Table: `departments`

┌─────────┬───────────────┐
│ dept_id │ dept_name │
├─────────┼───────────────┤
│ 101 │ 'Engineering' │
│ 102 │ 'Sales' │
│ 103 │ 'HR' │
└─────────┴───────────────┘

| 105 | 'Marketing' | -- (No employees in Marketing)

Table: `employees`

┌────────┬───────────┬─────────┐
│ emp_id │ emp_name │ dept_id │
├────────┼───────────┼─────────┤
│ 1 │ 'Alice' │ 101 │
│ 2 │ 'Bob' │ 101 │
│ 3 │ 'Charlie' │ 102 │
│ 4 │ 'Diana' │ 103 │
└────────┴───────────┴─────────┘

| 5 | 'Eve' | 104 | -- (No dept_id 104 in 'departments')

Now, let's write an INNER JOIN query to get employee names and their department names:

1 SELECT
2 e.emp_name,
3 d.dept_name
4 FROM
5 employees AS e -- 'e' is an alias for the employees table
6 INNER JOIN
7 departments AS d ON e.dept_id = d.dept_id; -- 'd' is an alias for the departments table

Result:

┌───────────┬───────────────┐
│ emp_name │ dept_name │
├───────────┼───────────────┤
│ 'Alice' │ 'Engineering' │
│ 'Bob' │ 'Engineering' │
│ 'Charlie' │ 'Sales' │
│ 'Diana' │ 'HR' │
└───────────┴───────────────┘

Explanation of the result:

- 'Eve' is excluded: Her dept_id (104) does not have a matching dept_id in the departments table.
- 'Marketing' is excluded: There are no employees with dept_id 105 in the employees table.
- Only rows that have a match in both tables are included in an INNER JOIN.
- Using table aliases (AS e, AS d) is a common and recommended practice to make your queries shorter and clearer,
  especially when dealing with multiple tables or columns with identical names.

4. Summary

An INNER JOIN is used to combine rows from two tables based on a matching column value. It only returns the rows where a
match is found in both tables, effectively showing the intersection of the two datasets.

`Checkpoint 4.3`: LEFT JOIN

1. Analogy: "Everyone from the First List"

Remember our INNER JOIN analogy where we only saw students who were enrolled in a course? A LEFT JOIN changes the rule.
It's like saying:

"Show me every single student from my main 'Students' list (the left list). If they happen to have an entry on the
'Courses' list (the right list), show me that course information next to their name. If they don't have an entry on the
'Courses' list, that's fine—just show their name and leave the course part blank."

The focus is on keeping everything from the first, or "left," list.

2. Concept: All Rows from the Left, Matches from the Right

The LEFT JOIN (also known as LEFT OUTER JOIN) is used when you want to keep all the rows from the "left" table, regardless
of whether they have a match in the "right" table.

- It retrieves all rows from the left table (table1).
- It retrieves the matching rows from the right table (table2).
- If a row from the left table has no matching row in the right table (based on the ON condition), the columns from the
  right table will appear as NULL in the result set.
- This is incredibly useful for finding things like "all customers and their orders, including customers who have never
  placed an order."

Syntax:
The syntax is almost identical to INNER JOIN, you just swap the keyword.
1 SELECT columns_you_want
2 FROM table1 -- This is the "left" table
3 LEFT JOIN table2 ON table1.matching_column = table2.matching_column;

3. Example

Let's use our familiar employees and departments tables.

Table: `employees` (The left table in our query)

┌────────┬───────────┬─────────┐
│ emp_id │ emp_name │ dept_id │
├────────┼───────────┼─────────┤
│ 1 │ 'Alice' │ 101 │
│ 2 │ 'Bob' │ 101 │
│ 3 │ 'Charlie' │ 102 │
│ 4 │ 'Diana' │ 103 │
└────────┴───────────┴─────────┘

| 5 | 'Eve' | 104 | -- (No dept_id 104 in 'departments')

Table: `departments` (The right table in our query)

┌─────────┬───────────────┐
│ dept_id │ dept_name │
├─────────┼───────────────┤
│ 101 │ 'Engineering' │
│ 102 │ 'Sales' │
│ 103 │ 'HR' │
└─────────┴───────────────┘

| 105 | 'Marketing' | -- (No employees in Marketing)

Now, let's write a LEFT JOIN query to get all employees and their department names, if they have one:

1 SELECT
2 e.emp_name,
3 d.dept_name
4 FROM
5 employees AS e
6 LEFT JOIN
7 departments AS d ON e.dept_id = d.dept_id;

Result:

┌───────────┬───────────────┐
│ emp_name │ dept_name │
├───────────┼───────────────┤
│ 'Alice' │ 'Engineering' │
│ 'Bob' │ 'Engineering' │
│ 'Charlie' │ 'Sales' │
│ 'Diana' │ 'HR' │
│ 'Eve' │ NULL │
└───────────┴───────────────┘

Explanation of the result:

- Notice that all five employees from the employees (left) table are included in the result.
- For 'Alice', 'Bob', 'Charlie', and 'Diana', a matching dept_id was found in the departments table, so their dept_name is
  shown.
- For 'Eve', her dept_id of 104 did not find a match in the departments table. Instead of being excluded (like in an INNER
  JOIN), her row is kept, and the dept_name column from the departments table is filled with NULL.
- The 'Marketing' department is still not in the result, because no employee in the left table links to it.

4. Summary

A LEFT JOIN returns all rows from the left table and only the matching rows from the right table. When a row in the left
table has no match in the right table, the columns from the right table are shown as NULL. This is perfect for when you
want to see everything from one table, plus any related information from another.

`Checkpoint 4.5`: FULL OUTER JOIN

1. Analogy: "Everything from Both Lists"

We've seen JOINs that act like this:

- `INNER JOIN`: Only give me items that are on both List A and List B.
- `LEFT JOIN`: Give me everything from List A, and if there's a match on List B, show it.
- `RIGHT JOIN`: Give me everything from List B, and if there's a match on List A, show it.

Now, FULL OUTER JOIN is the "show me everything" option. It's like saying:

"Take all the items from List A and all the items from List B and put them together. If an item from List A matches an item
from List B, put them on the same line. If an item from either list doesn't have a match, that's fine, just put it on its
own line and leave the other list's columns blank."

2. Concept: All Rows from Both Tables

The FULL OUTER JOIN (or simply FULL JOIN in some databases) combines the results of both LEFT JOIN and RIGHT JOIN. It's the
most inclusive type of join.

- It retrieves all rows from both tables.
- If a row from table1 (the left table) has a match in table2 (the right table), it combines them.
- If a row from the left table has no match in the right table, it is still included, and the columns from the right table
  are filled with NULL.
- If a row from the right table has no match in the left table, it is also included, and the columns from the left table
  are filled with NULL.
- This is useful for when you need to see all data from two tables at once, especially to find discrepancies or orphans in
  either table.

Syntax:

1 SELECT columns_you_want
2 FROM table1
3 FULL OUTER JOIN table2 ON table1.matching_column = table2.matching_column;

3. Example

Let's use our final example with the employees and departments tables.

Table: `employees` (The "left" table)

┌────────┬───────────┬─────────┐
│ emp_id │ emp_name │ dept_id │
├────────┼───────────┼─────────┤
│ 1 │ 'Alice' │ 101 │
│ 2 │ 'Bob' │ 101 │
│ 3 │ 'Charlie' │ 102 │
│ 4 │ 'Diana' │ 103 │
└────────┴───────────┴─────────┘

| 5 | 'Eve' | 104 | -- (No dept_id 104 in 'departments')

Table: `departments` (The "right" table)

┌─────────┬───────────────┐
│ dept_id │ dept_name │
├─────────┼───────────────┤
│ 101 │ 'Engineering' │
│ 102 │ 'Sales' │
│ 103 │ 'HR' │
└─────────┴───────────────┘

| 105 | 'Marketing' | -- (No employees in this dept)

Now, let's write a FULL OUTER JOIN to see every employee and every department.

1 SELECT
2 e.emp_name,
3 d.dept_name
4 FROM
5 employees AS e
6 FULL OUTER JOIN
7 departments AS d ON e.dept_id = d.dept_id;

Result:

┌───────────┬───────────────┐
│ emp_name │ dept_name │
├───────────┼───────────────┤
│ 'Alice' │ 'Engineering' │
│ 'Bob' │ 'Engineering' │
│ 'Charlie' │ 'Sales' │
│ 'Diana' │ 'HR' │
│ 'Eve' │ NULL │
│ NULL │ 'Marketing' │
└───────────┴───────────────┘

Explanation of the result:

- The first four rows are the matching records found in an INNER JOIN.
- The row for 'Eve' is included because LEFT JOIN logic includes all rows from the left table. Since she has no matching
  department, dept_name is NULL.
- The row for 'Marketing' is included because RIGHT JOIN logic includes all rows from the right table. Since it has no
  matching employees, emp_name is NULL.

4. Summary

A FULL OUTER JOIN returns all rows when there is a match in one of the tables. It's essentially a LEFT JOIN and a RIGHT
JOIN combined. This is the join to use when you want to see all data from both tables, regardless of whether a match exists
in the other table.

`Checkpoint 4.6`: Aggregates

Analogy:
Imagine you have a class full of students, and each student has a test score. If you want to know each student's individual score,
you'd read them one by one. But if you want to know things about the entire class or groups of students, like the highest score, the
average score, or how many students took the test, you'd use aggregate functions. They take all the individual scores and condense
them into a single, summary piece of information.

Concept:
Aggregate functions (sometimes called set functions) operate on a collection of rows and return a single summary value. They are
typically used to perform calculations like counting items, summing values, or finding averages, minimums, or maximums within a
dataset. While often used with GROUP BY (which we'll cover soon), they can also summarize an entire table.

Common aggregate functions include:

- COUNT(): Returns the number of rows or non-NULL values.
- SUM(): Calculates the total sum of a numeric column.
- AVG(): Computes the average value of a numeric column.
- MIN(): Finds the smallest value in a column.
- MAX(): Finds the largest value in a column.

Example:
Let's use our Inventory table:

Table: `Inventory`

┌──────────┬─────────┬─────────────┐
│ store_id │ book_id │ stock_count │
├──────────┼─────────┼─────────────┤
│ 101 │ 1 │ 5 │
│ 102 │ 2 │ 10 |
│ 101 │ 4 │ 3 │
└──────────┴─────────┴─────────────┘

1.  Count all inventory entries:
    1 SELECT COUNT(\*) AS total_inventory_entries
    2 FROM Inventory;
    Result:
    | total_inventory_entries |
    | :---------------------- |
    | 3 |

2.  Sum of all `stock_count` values:
    1 SELECT SUM(stock_count) AS total_stock
    2 FROM Inventory;
    Result:
    | total_stock |
    | :---------- |
    | 18 | (5 + 10 + 3)

3.  Average `stock_count`:

1 SELECT AVG(stock_count) AS average_stock
2 FROM Inventory;
Result:
| average_stock |
| :------------ |
| 6.0 | (18 / 3)

4.  Minimum `stock_count`:

1 SELECT MIN(stock_count) AS min_stock
2 FROM Inventory;
Result:
| min_stock |
| :-------- |
| 3 |

5.  Maximum `stock_count`:

1 SELECT MAX(stock_count) AS max_stock
2 FROM Inventory;
Result:
| max_stock |
| :-------- |
| 10 |

Summary:
Aggregate functions are powerful tools to gain insights from your data by performing calculations across rows, providing a
summarized view rather than individual details.

`Checkpoint 4.7`: GROUP BY

Analogy:
Think back to our classroom with student test scores. With aggregate functions, we could find the average score for the entire class. Now, imagine you
want to find the average score for each homeroom or for each grade level. To do this, you would first separate the students into their respective
homerooms or grade levels (that's the "grouping"), and then calculate the average score within each of those smaller groups. The GROUP BY clause does
exactly this: it divides your data into groups to perform summary calculations on each group.

Concept:
The GROUP BY clause in SQL is used to collect data from multiple records and group them together based on one or more columns. It's almost always used in
conjunction with aggregate functions (like COUNT, SUM, AVG, MIN, MAX) to perform calculations for each group independently.

A crucial rule to remember: Any column in your `SELECT` list that is _not_ an aggregate function _must_ also be included in your `GROUP BY` clause. This
tells the database which characteristics to use for forming the distinct groups.

Syntax:

1 SELECT column_to_group_by, aggregate_function(column_to_aggregate)
2 FROM table_name
3 WHERE condition_optional
4 GROUP BY column_to_group_by(s)
5 ORDER BY column_to_sort_by_optional;

Example:
Let's use our Sales table again:

Table: `Sales`

┌─────────┬────────────┬───────────────┬────────────────┐
│ sale_id │ product_id │ quantity_sold │ price_per_unit │
├─────────┼────────────┼───────────────┼────────────────┤
│ 1 │ 101 │ 2 │ 10.00 │
│ 2 │ 102 │ 1 │ 25.50 │
│ 3 │ 101 │ 3 │ 10.00 │
│ 4 │ 103 │ 5 │ 5.00 │
│ 5 │ 102 │ 2 │ 25.50 │
│ 6 │ 101 │ 1 │ 10.00 │
└─────────┴────────────┴───────────────┴────────────────┘

Question: What is the total quantity sold for each unique product?

To answer this, we need to group the sales data by product_id.

Query:
1 SELECT
2 product_id,
3 SUM(quantity_sold) AS total_quantity_per_product
4 FROM
5 Sales
6 GROUP BY
7 product_id;

How this query works:

1.  The FROM Sales clause targets our data.
2.  GROUP BY product_id instructs the database to gather all rows with the same product_id into distinct groups.
    - Group 1: All sales for product_id = 101
    - Group 2: All sales for product_id = 102
    - Group 3: All sales for product_id = 103
3.  Then, for each of these groups, the SELECT clause retrieves the product_id and calculates SUM(quantity_sold) only for the rows within that specific
    group.

Result:
| 101 | 6 | (2 + 3 + 1 from sales with product_id 101)
| 102 | 3 | (1 + 2 from sales with product_id 102)
| 103 | 5 | (5 from sales with product_id 103)

Summary:
The GROUP BY clause is essential for performing aggregate calculations on specific categories or subdivisions of your data, allowing you to derive
meaningful insights on a per-group basis.

`Checkpoint 4.8`: HAVING vs WHERE

Analogy:
Think of filtering students in a classroom again:

- `WHERE` Clause: You want to find students who scored above 70 on a test. You look at each student's individual score and decide if they meet the
  criteria before you start doing any kind of grouping or calculating averages. This filters the individual records.

- `HAVING` Clause: Now, imagine you've already grouped students by their homeroom and calculated the average score for each homeroom. You then want to
  find only those homerooms where the average score of the homeroom is above 80. Here, you are filtering the groups themselves based on a condition that
  involves an aggregate (the average score of the group). This filters the summarized groups.

Concept:
Both WHERE and HAVING clauses are used for filtering data in SQL, but they serve different purposes and operate at different stages of a query's
execution.

- `WHERE` Clause:
  - Filters individual rows. It evaluates conditions on each row before any grouping occurs and before aggregate functions are applied.
  - Cannot use aggregate functions. Conditions must be based on values directly available in the individual rows.
  - It's applied early in the query process.

- `HAVING` Clause:
  - Filters groups of rows. It evaluates conditions on the results of the GROUP BY clause, after groups have been formed and aggregate functions have
    been calculated.
  - Can use aggregate functions. This is its primary purpose – to filter based on aggregate results (e.g., HAVING COUNT(\*) > 5).
  - It's applied later in the query process, after GROUP BY.

Simplified SQL Query Logical Execution Order (Relevant parts):

1.  FROM (Identifies the tables)
2.  WHERE (Filters individual rows based on conditions)
3.  GROUP BY (Organizes filtered rows into groups)
4.  HAVING (Filters these groups based on conditions, often involving aggregates)
5.  SELECT (Determines which columns/aggregates to display)

Example:
Using our Employees table:

Table: `Employees`

┌─────────────┬─────────┬─────────────┬──────────┐
│ employee_id │ name │ department │ salary │
├─────────────┼─────────┼─────────────┼──────────┤
│ 1 │ Alice │ HR │ 60000.00 │
│ 2 │ Bob │ Engineering │ 80000.00 │
│ 3 │ Charlie │ HR │ 65000.00 │
│ 4 │ Diana │ Engineering │ 90000.00 │
│ 5 │ Eve │ Marketing │ 70000.00 │
│ 6 │ Frank │ Engineering │ 75000.00 │
└─────────────┴─────────┴─────────────┴──────────┘

Scenario 1: Using `WHERE` (Filtering individual rows)
Question: Find the name and salary of employees whose salary is exactly $70,000.

1 SELECT name, salary
2 FROM Employees
3 WHERE salary = 70000;
Result:

┌──────┬──────────┐
│ name │ salary │
├──────┼──────────┤
│ Eve │ 70000.00 │
└──────┴──────────┘

Explanation: The WHERE clause checks each employee's salary individually before any grouping or aggregation.

Scenario 2: Using `HAVING` (Filtering groups based on aggregates)
Question: Find departments where the average salary is greater than $70,000.

1 SELECT department, AVG(salary) AS avg_dept_salary
2 FROM Employees
3 GROUP BY department
4 HAVING AVG(salary) > 70000;
Result:

┌─────────────┬─────────────────┐
│ department │ avg_dept_salary │
├─────────────┼─────────────────┤
│ Engineering │ 81666.67 │
└─────────────┴─────────────────┘

Explanation:

1.  Rows are grouped by department.
2.  AVG(salary) is calculated for each group (HR: $62500, Engineering: $81666.67, Marketing: $70000).
3.  The HAVING clause then filters these groups, keeping only "Engineering" because its average salary ($81666.67) is greater than $70,000.

Scenario 3: Using both `WHERE` and `HAVING`
Question: Find departments where the average salary of employees who earn more than $60,000 is greater than $75,000.

1 SELECT department, AVG(salary) AS avg_filtered_salary
2 FROM Employees
3 WHERE salary > 60000 -- Filters out individual employees (e.g., Alice) first
4 GROUP BY department
5 HAVING AVG(salary) > 75000; -- Then filters the groups based on their calculated average
Result:

┌─────────────┬─────────────────────┐
│ department │ avg_filtered_salary │
├─────────────┼─────────────────────┤
│ Engineering │ 81666.67 │
└─────────────┴─────────────────────┘

Explanation: The WHERE clause first filters out Alice. Then, the remaining employees are grouped by department, their average salaries are calculated, and
finally, HAVING filters these groups to find those with an average greater than $75,000.

Summary:

- `WHERE` filters rows before GROUP BY and aggregates. It operates on individual row data.
- `HAVING` filters groups after GROUP BY and aggregates. It operates on the summarized group data, often using aggregate functions.

Understanding the sequence of operations (FROM -> WHERE -> GROUP BY -> HAVING -> SELECT) is key to mastering these clauses.
