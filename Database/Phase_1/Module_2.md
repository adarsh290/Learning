`Checkpoint 2.2`: Numbers (INT, BIGINT, NUMERIC).

1. Integer Types (For Whole Numbers)

- `SMALLINT`: This is like a small coffee cup. It can hold
  relatively small whole numbers, typically from -32,768
  to +32,767.
  - Use for: Things like age (if you're sure it won't
    exceed 32k), counts of small items, or enum-like
    IDs.
  - Benefit: Uses less storage space.

- `INTEGER` (or `INT`): This is your standard-sized coffee
  mug. It's the most common choice for general-purpose
  whole numbers, typically from -2 billion to +2 billion.
  - Use for: Most IDs (like product_id, user_id), counts
    that won't exceed 2 billion, or scores.
  - Benefit: Good balance of range and storage. Usually
    a safe default.

- `BIGINT`: This is a large thermos. It can store very,
  very large whole numbers, from about -9 quintillion to
  +9 quintillion.
  - Use for: Extremely large IDs, high-volume counters
    (e.g., website hits), or timestamps stored as
    milliseconds since an epoch.
  - Benefit: Needed for truly massive numbers.

2. Arbitrary Precision Numbers (NUMERIC / DECIMAL)

- `NUMERIC(precision, scale)`:
  - precision: The total number of digits (both before
    and after the decimal point).
  - scale: The number of digits after the decimal point.
  - Example: NUMERIC(6, 2) can store a number like
    1234.56 (4 digits before, 2 after, total 6 digits).
    If you try to store 12345.67, it would result in an
    error or truncation depending on specific settings,
    as it exceeds 6 total digits.
  - Use for: Money (always use this for financial
    data!), scientific measurements, or any situation
    where even tiny rounding errors are unacceptable.
  - Benefit: Guarantees exact storage and calculations.
  - Downside: Calculations can be slightly slower than
    floating-point numbers.

3. Floating-Point Types (REAL, DOUBLE PRECISION)

- `REAL`: Single-precision floating-point number.
- `DOUBLE PRECISION`: Double-precision floating-point
  number (more precise than REAL).
- Use for: Scientific calculations, engineering data,
  graphics coordinates, or anything where the occasional
  minuscule rounding difference won't break your
  application.
- Crucial Point: NEVER use `REAL` or `DOUBLE PRECISION`
  for money or other financial calculations. The tiny
  rounding errors inherent in floating-point math can lead
  to serious problems when exactness is required.

`Checkpoint 2.3`: Text (VARCHAR, TEXT, CHAR)!

1. CHAR(n) (Fixed-length character string)

- Fixed Length: Stores strings of precisely n characters.
- Padding: If you store a string shorter than n,
  PostgreSQL will automatically pad it with spaces to
  reach n length.
- Truncation/Error: If you try to store a string longer
  than n, it will usually result in an error (or be
  truncated, depending on strictness settings).
- Use Cases: Very rarely used in modern databases. Might
  be considered for strict, fixed-length codes, like a
  two-letter country code (e.g., 'US', 'GB') where the
  length is always exactly 2 and leading/trailing spaces
  are explicitly desired or handled.
- Key Takeaway: Generally avoid this unless you have a
  very specific, strict fixed-length requirement where
  padding is expected. The padding can lead to unexpected
  issues in comparisons.

2. VARCHAR(n) (Variable-length character string with limit)

- Variable Length, Max `n`: Stores strings up to a maximum
  length of n characters.
- Efficient Space: Only consumes storage equal to the
  actual string's length.
- Truncation/Error: If you try to store a string longer
  than n, it's an error.
- Use Cases: The most common choice for text fields where
  you expect the length to vary but have a reasonable
  upper bound. Examples: names (VARCHAR(100)), short
  descriptions (VARCHAR(255)), email addresses
  (VARCHAR(255)), titles.
- Key Takeaway: This is your go-to for most shorter,
  variable-length text fields. The n acts as a constraint
  to prevent overly long data from being entered.

3. TEXT (Variable-length character string, unlimited)

- Variable Length, No Limit: Stores strings of virtually
  any length. You don't specify n.
- Efficient Space: Like VARCHAR, it only consumes storage
  equal to the actual string's length.
- Use Cases: For potentially very long strings where you
  don't know the maximum length, or where the maximum
  could be extremely large. Examples: article bodies, user
  comments, product descriptions, JSON or XML data.
- Key Takeaway: In modern PostgreSQL, TEXT is often just
  as efficient as VARCHAR internally for many operations.
  Use it when you don't need a strict length constraint or
  expect very long strings.

`Checkpoint 2.4`: Date/Time data types in PostgreSQL!

1. DATE

- What it stores: Only the date (year, month, day). It
  does not include any time-of-day information.
- Use cases: Birthdays, anniversaries, publication dates,
  or any instance where the specific time of day is
  irrelevant.
- Example: '2026-02-13'

2. TIME (or TIME WITHOUT TIME ZONE)

- What it stores: Only the time of day (hour, minute,
  second, and fractional seconds). It does not include any
  date information.
- Use cases: Storing a recurring event time (e.g., "store
  opens at 9:00 AM"), alarm times, or scheduling.
- Example: '14:30:00' (for 2:30 PM)

3. TIMESTAMP (or TIMESTAMP WITHOUT TIME ZONE)

- What it stores: Both the date and time (year, month,
  day, hour, minute, second, and fractional seconds).
- Crucial Point: It explicitly does NOT store any time
  zone information. When you insert a TIMESTAMP value,
  it's stored exactly as you provide it. When you retrieve
  it, you get exactly what you stored. Any time zone
  interpretation is left entirely to the application or
  the client.
- Use cases: When all your users and servers are
  guaranteed to be in the same time zone, or when the time
  zone context is irrelevant (e.g., recording elapsed time
  locally, where conversion is never needed). Use with
  caution in global applications.
- Example: '2026-02-13 14:30:00'

4. TIMESTAMPTZ (or TIMESTAMP WITH TIME ZONE)

- What it stores: Both the date and time, with awareness
  of time zone.
- How it works: When you insert a TIMESTAMPTZ value,
  PostgreSQL converts it to UTC (Coordinated Universal
  Time) internally and stores it as UTC. When you retrieve
  the value, PostgreSQL converts it back from UTC to the
  time zone of your current session (or the server's
  default if no session time zone is set).
- Use cases: This is generally the most recommended type
  for recording events in a global application. User
  activity logs, transaction timestamps, when it's
  critical to know the exact moment an event occurred
  relative to UTC and display it correctly to users in
  different time zones.
- Example: If your session is 'America/Los_Angeles' (PST)
  and you insert '2026-02-13 14:30:00 PST', it will be
  stored internally as '2026-02-13 22:30:00 UTC'. If you
  then retrieve it from a session set to 'Europe/London'
  (GMT), it would display as '2026-02-13 22:30:00 GMT'.

5. INTERVAL

- What it stores: A duration of time.
- Use cases: Storing "3 days 2 hours", "1 month 15
  minutes", or calculating the difference between two
  TIMESTAMP values.
- Example: '1 year 2 months 3 days 4 hours 5 minutes 6
  seconds'

`Checkpoint 2.5`: Primary Keys!

Primary Key Recap (from Module 1)

- A Primary Key is a column (or a set of columns) that
  uniquely identifies each row in a table.
- It must always contain unique values (no duplicates).
- It must never contain NULL values (it's always present).

Implementing Primary Keys in PostgreSQL

You define a primary key using the PRIMARY KEY constraint
in your CREATE TABLE statement.

1. `PRIMARY KEY` Constraint:

- Column-level definition: Applied directly after the
  column's data type.

- Table-level definition: Defined after all column
  definitions. This is necessary for composite primary
  keys (PKs made of multiple columns) or when you want to
  name the constraint.

2. Generating Primary Key Values (Auto-Incrementing IDs):

Manually assigning primary keys can be error-prone (you
might accidentally create duplicates). PostgreSQL provides
excellent ways to auto-generate unique identifiers:

- `SERIAL` and `BIGSERIAL`:
  - These are pseudo-types that automatically create an
    auto-incrementing integer column.
  - **SERIAL** effectively creates an INTEGER column and
    links it to a sequence object, which generates
    unique, sequential numbers starting from 1 for each
    new row.
  - **BIGSERIAL** does the same but for BIGINT, providing a
    much larger range of numbers.
  - When to use: Most common choice for simple,
    non-meaningful (surrogate) primary keys where a
    sequential number is fine. BIGSERIAL is often
    preferred for future-proofing as it provides a
    larger range.

- `UUID` (Universally Unique Identifier):
  - A UUID is a 128-bit number that is practically
    guaranteed to be unique across all databases, all
    computers, and even all time. It looks like
    a1b2c3d4-e5f6-7890-1234-567890abcdef.
  - When to use: For distributed systems, when merging
    data from multiple sources, or when you want primary
    keys that don't reveal the number of rows in your
    table (unlike sequential SERIAL IDs).
  - Setup: You usually need to enable an extension
    first: CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

Best Practices for Primary Keys:

- Every table should have a primary key. It's fundamental
  for data integrity and relating tables.
- Prefer simple, non-meaningful (surrogate) keys like
  SERIAL/BIGSERIAL or UUID unless a "natural key" (a
  meaningful column like a product_code if it's truly
  unique and stable) perfectly fits all PK criteria.
- BIGSERIAL is often a good default choice for new tables
  over SERIAL to avoid potential overflow issues in very
  large tables.

`Checkpoint 2.6`: Foreign Keys!

In Module 1, we learned about normalizing tables to remove
redundancy. But how do we link those separate tables back together?
How do we ensure that a post_id in a post_tags table actually refers
to a post_id that exists in the blog_posts table? The answer is
Foreign Keys.

What is a Foreign Key (FK)?

Think of a foreign key as a "cross-reference" or a "lookup"
mechanism between tables.

- Purpose: Foreign Keys are used to enforce referential integrity
  between tables. This means maintaining consistency and valid
  relationships between data. They ensure that relationships
  between tables are always valid.
- Definition: A Foreign Key is a column (or a set of columns) in
  one table (the "referencing" or "child" table) that refers to the
  Primary Key (or sometimes a Unique Key) in another table (the
  "referenced" or "parent" table).
- The Rules of Referential Integrity:
  1.  The data types of the foreign key column(s) in the child
      table must exactly match the data types of the primary key
      column(s) in the parent table.
  2.  A value in the foreign key column(s) of the child table must
      either exist as a value in the referenced primary key
      column(s) of the parent table or be NULL (if the foreign key
      column is allowed to be NULL). You cannot have an author_id
      in your blog_posts table that doesn't correspond to an
      actual author_id in your authors table (if you had one).

Implementing Foreign Keys in PostgreSQL

You define a foreign key using the FOREIGN KEY constraint. It's
usually defined at the table level after all column definitions,
giving it a name for easier management.

Syntax Example:
1 CREATE TABLE authors (
2 author_id SERIAL PRIMARY KEY,
3 author_name VARCHAR(100) NOT NULL
4 );
5
6 CREATE TABLE blog_posts (
7 post_id BIGSERIAL PRIMARY KEY,
8 post_title VARCHAR(200) NOT NULL,
9 author_id INTEGER, -- Matches data type of authors.author_id
10 publish_date DATE,
11
12 -- Define the foreign key constraint
13 CONSTRAINT fk_author -- Naming the constraint is good practi
14 FOREIGN KEY (author_id) -- This column in blog_posts is the FK
15 REFERENCES authors (author_id) -- It refers to the author_id in the authors table
16 -- Optional: ON DELETE and ON UPDATE actions go here (explained below)
17 );

ON DELETE and ON UPDATE Actions (Referential Actions)

These clauses specify what PostgreSQL should do to the child table's
rows when the corresponding parent row's primary key is deleted or
updated. These are crucial for maintaining data integrity.

1.  `NO ACTION` (Default) / `RESTRICT`:
    - What it does: PostgreSQL will reject the deletion or update
      operation on the parent table if there are any referencing
      child rows. This prevents "orphaned" records.
    - When to use: When you absolutely don't want parent records to
      be modified or deleted if they are still being referenced.

2.  `CASCADE`:
    - What it does: If a parent row is deleted or its primary key
      is updated, all corresponding child rows are automatically
      deleted or updated as well.
    - When to use: When the child data is meaningless without the
      parent. For example, if you delete an Order, you likely want
      all its order_items to be deleted too. If post_id changes in
      blog_posts, you'd want post_tags.post_id to update
      automatically.

3.  `SET NULL`:
    - What it does: If a parent row is deleted or its primary key
      is updated, the foreign key column(s) in the child row(s) are
      automatically set to NULL.
    - Requirement: The foreign key column in the child table must
      be nullable (dept_id INTEGER not dept_id INTEGER NOT NULL).
    - When to use: When the child record can still exist
      meaningfully without a direct parent link, but loses its
      specific association. For example, if an employee record has
      a manager_id, and that manager is deleted, the employee might
      still exist but now has no assigned manager.

4.  `SET DEFAULT`:
    - What it does: If a parent row is deleted or its primary key
      is updated, the foreign key column(s) in the child row(s) are
      automatically set to their predefined default value.
    - Requirement: The foreign key column in the child table must
      have a DEFAULT value specified.
    - When to use: Similar to SET NULL, but you want a specific
      default value assigned instead of just NULL.

Example with ON DELETE / ON UPDATE

    1 CREATE TABLE departments (
    2     dept_id SERIAL PRIMARY KEY,
    3     dept_name VARCHAR(100) NOT NULL UNIQUE
    4 );
    5
    6 CREATE TABLE employees (
    7     emp_id SERIAL PRIMARY KEY,
    8     emp_name VARCHAR(100) NOT NULL,
    9     dept_id INTEGER, -- This column will be our FK, and it's nullable

10
11 CONSTRAINT fk_department
12 FOREIGN KEY (dept_id) -- The 'dept_id' in employees is t FK
13 REFERENCES departments (dept_id) -- It points to 'dept_i in the 'departments' table
14 ON DELETE SET NULL -- If a department is deleted, employees in it will have their dept_id set to NULL
15 ON UPDATE CASCADE -- If a department's dept_id changes, employees' dept_id will automatically update
16 );

`Checkpoint 2.7`: Constraints (UNIQUE, NOT NULL, CHECK, DEFAULT)!

Constraints are powerful tools in PostgreSQL (and SQL databases in
general) that allow you to enforce rules on the data in your tables.
They ensure data accuracy, consistency, and reliability by
preventing invalid data from being entered into the database.

Think of them as built-in quality control rules for your data entry
forms.

1. UNIQUE Constraint

- Purpose: Ensures that all values in a specified column (or a
  group of columns) are distinct. No two rows can have the same
  value(s) in that column (or combination of columns), except for
  NULL values (multiple NULLs are allowed unless also NOT NULL).
- Difference from `PRIMARY KEY`: A PRIMARY KEY is implicitly UNIQUE
  and NOT NULL. A UNIQUE constraint allows NULL values and you can
  have multiple UNIQUE constraints on a table, but only one PRIMARY
  KEY.
- Syntax:
  - Column-level: column_name data_type UNIQUE
  - Table-level (for named constraints or multiple columns):

1 CREATE TABLE users (
2 user_id SERIAL PRIMARY KEY,
3 username VARCHAR(50) NOT NULL,
4 email VARCHAR(255),
5 CONSTRAINT unique_username UNIQUE (username),
6 CONSTRAINT unique_email UNIQUE (email) -- Allows NULL email, but unique for non-NULL
7 );

- Use Cases: Usernames, email addresses, social security numbers
  (if not chosen as PK), product SKUs.

2. NOT NULL Constraint

- Purpose: Ensures that a column cannot store NULL values. If a
  column is defined as NOT NULL, you must provide a value for it
  when inserting a new row, and you cannot update it to NULL.
- Syntax: column_name data_type NOT NULL
- Use Cases: Any essential piece of data that must always be
  present (e.g., product_name, order_date, author_name).

3. CHECK Constraint

- Purpose: Ensures that all values in a column satisfy a specific
  Boolean condition. If an inserted or updated value violates the
  condition, the operation is rejected.
- Syntax:
  - Column-level: column_name data_type CHECK (condition)
  - Table-level (for named constraints or conditions involving
    multiple columns):


    1         CREATE TABLE products (
    2             product_id SERIAL PRIMARY KEY,
    3             price NUMERIC(10,2) CHECK (price >= 0), -- Price cannot be negative
    4             stock INTEGER CHECK (stock >= 0 AND stock <= 1000) - Stock between 0 and 1000
    5         );
    6
    7         CREATE TABLE orders (
    8             order_id SERIAL PRIMARY KEY,
    9             start_date DATE,

10 end_date DATE,
11 CONSTRAINT check_date_order CHECK (end_date >=start_date) -- End date must be after start date
12 );

- Use Cases: Enforcing age limits (age >= 18), salary ranges, valid
  status values (status IN ('active', 'inactive')), ensuring dates
  are chronologically correct.

4. DEFAULT Constraint

- Purpose: Provides a default value for a column when no value is
  explicitly specified during an INSERT operation. If you don't
  provide a value for a column with a DEFAULT constraint,
  PostgreSQL will automatically insert the default value.
- Syntax: column_name data_type DEFAULT default_value
- Use Cases:
  - Setting created_at or updated_at timestamps to the current
    time (DEFAULT NOW()).
  - Assigning a default status (e.g., status VARCHAR(20) DEFAULT
    'pending').
  - Boolean flags (e.g., is_active BOOLEAN DEFAULT TRUE).

Example CREATE TABLE with all Constraints:

1 CREATE TABLE items (
2 item_id SERIAL PRIMARY KEY,
3 name VARCHAR(100) NOT NULL UNIQUE, -- Name must exist and be unique
4 description TEXT DEFAULT 'No description provided.', -- Default description if none given
5 price NUMERIC(8,2) CHECK (price > 0), -- Price must be positive
6 quantity_on_hand INTEGER CHECK (quantity_on_hand >= 0), --Quantity cannot be negative
7 item_status VARCHAR(10) DEFAULT 'available' CHECK (item_status IN ('available', 'sold', 'damaged')), -- Default status, limited choices
8 created_at TIMESTAMPTZ DEFAULT NOW() -- Auto-set creation timestamp
9 );
