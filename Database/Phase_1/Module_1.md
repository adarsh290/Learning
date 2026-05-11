`Checkpoint 1.1` - Sets & Tuples.

- Set: An unordered collection of unique items.
- Tuple: An ordered list of items, where the order and
  position of each item are significant.

`Checkpoint 1.2`: Relations & Attributes!

┌─────────┬──────────────┬─────────────────────┐
│ Name │ Phone Number │ Email │
├─────────┼──────────────┼─────────────────────┤
│ Alice │ 555-1234 │ alice@example.com │
│ Bob │ 555-5678 │ bob@example.com │
│ Charlie │ 555-9012 │ charlie@example.com │
└─────────┴──────────────┴─────────────────────┘

Relations (Tables)

The entire spreadsheet you see above is what we call a
Relation. In simpler terms, a Relation is essentially a
Table.

1.  A Set of Tuples:
    - Each row in your table is a tuple.
    - Just like in a set, each entire row (tuple) in a
      relation must be unique. You can't have two
      identical rows in a table.
    - The order of the rows doesn't matter (just like
      elements in a set don't have a defined order).
2.  Has a Name: Every relation (table) has a name, like
    Contacts, Students, or Products.
3.  Defined Structure: It has a specific number of columns,
    and each column has a name and stores a specific type
    of information.

Attributes (Columns)

Now, look at the headers in your spreadsheet: "Name,"
"Phone Number," "Email." These are what we call Attributes.
In simpler terms, an Attribute is a Column in a table.

Imagine you want to store information about students in a
database.

`Checkpoint 1.3`: First Normal Form (1NF)!

What is First Normal Form (1NF)?

A table (or relation) is in First Normal Form (1NF) if it
meets two fundamental conditions:

1.  Atomic Values: Each column must contain only atomic
    (indivisible) values. This means:
    - You cannot have a single cell holding multiple
      values (e.g., a list of phone numbers like
      "555-1234, 555-5678").
    - You cannot have "repeating groups" of columns (e.g.,
      Phone1, Phone2, Phone3 as separate columns for the
      same concept). Each piece of information should be
      in its own cell, and that cell should represent a
      single, indivisible data point.
2.  Unique Rows: Each row (tuple) in the table must be
    unique. This goes back to our definition of a relation
    as a set of tuples – sets, by definition, don't have
    duplicate elements.

Example: Violating 1NF

Let's say we have a table called Orders for an online
store:

┌─────────┬──────────────┬───────────────┬──────────┐
│ OrderID │ CustomerName │ ItemPurchased │ Quantity │
├─────────┼──────────────┼───────────────┼──────────┤
│ 101 │ Alice │ Laptop, Mouse │ 1, 1 │
│ 102 │ Bob │ Keyboard │ 1 │
└─────────┴──────────────┴───────────────┴──────────┘

Is this table in 1NF? No!

- The ItemPurchased column contains multiple values
  ("Laptop, Mouse") for OrderID 101.
- The Quantity column also contains multiple values ("1,
  1") for OrderID 101.

These are examples of multi-valued attributes, violating
the atomic values rule of 1NF. It would be very hard to
query "all orders that included a Mouse" or "total quantity
of Laptops sold" with this structure.

Example: How to Achieve 1NF

To bring the Orders table into 1NF, we need to ensure each
cell holds only one atomic value. We do this by breaking
down the multi-valued attributes, often by repeating
information for each individual item:

┌─────────┬──────────────┬───────────────┬──────────┐
│ OrderID │ CustomerName │ ItemPurchased │ Quantity │
├─────────┼──────────────┼───────────────┼──────────┤
│ 101 │ Alice │ Laptop │ 1 │
│ 101 │ Alice │ Mouse │ 1 │
│ 102 │ Bob │ Keyboard │ 1 │
└─────────┴──────────────┴───────────────┴──────────┘

Now:

- Each cell contains a single, atomic value (e.g.,
  'Laptop' is one item, '1' is one quantity).
- Each row is unique ((101, Alice, Laptop, 1) is distinct
  from (101, Alice, Mouse, 1)).

This table is now in First Normal Form!

You've already shown a great understanding of the first
rule and why multi-valued attributes are problematic. Now
that you know the second rule, let's practice applying
both.

`Checkpoint 1.4`: Second Normal Form (2NF)!

Before we define 2NF, we need to understand two key
concepts:

1. Primary Key

You've learned that each row in a 1NF table must be unique.
But how do we guarantee and identify that uniqueness?
That's where a Primary Key comes in.

- A Primary Key (PK) is a column, or a set of columns,
  whose values uniquely identify each row in a table.
- Rules for a Primary Key:
  - Unique: No two rows can have the same primary key
    value.
  - Non-NULL: A primary key column cannot contain NULL
    (empty) values.
  - Minimal: It should contain the fewest possible
    columns to ensure uniqueness.

- Example: In a Students table, StudentID might be the
  primary key. If you know the StudentID, you can identify
  exactly one student.
- Composite Primary Key: Sometimes, a single column isn't
  enough to uniquely identify a row. You might need to
  combine two or more columns. For instance, in a Grades
  table, StudentID alone isn't unique (a student can have
  many grades), and CourseID alone isn't unique. But the
  combination (StudentID, CourseID) might uniquely
  identify a specific grade for a specific student in a
  specific course. This combination of columns is called a
  Composite Primary Key.

2. Functional Dependency

This is a fancy term for a simple idea:

- An attribute (column) B is functionally dependent on an
  attribute (column) A if the value of A uniquely
  determines the value of B.
- We write this as A -> B (read as "A determines B").

A table is in Second Normal Form (2NF) if and only if it
satisfies both of the following conditions:

1.  It is already in First Normal Form (1NF). (This is a
    prerequisite).
2.  All non-key attributes are fully functionally dependent
    on the entire Primary Key.

Let's break down the second condition:

- Non-key attributes: These are any attributes in the
  table that are not part of the Primary Key.
- Fully functionally dependent: This is important when you
  have a composite primary key (a primary key made of two
  or more columns). It means that a non-key attribute
  cannot depend on only a part of the composite primary
  key. If it depends on only a part of the primary key,
  it's called a partial dependency, and that violates 2NF.

Example: Violating 2NF (Partial Dependency)

Let's use an example of an OrderDetails table, which tracks
items in customer orders.
Suppose the Primary Key for OrderDetails is a composite
key: (OrderID, ProductID).

┌───────┬────────┬─────┬───────┬───────┬───────┬───────┐
│ Orde… │ Produ… │ Qua │ Prod… │ Prod… │ Cust… │ Cust… │
├───────┼────────┼─────┼───────┼───────┼───────┼───────┤
│ 1 │ 101 │ 2 │ Lapt… │ 1200 │ Alice │ New … │
│ 1 │ 102 │ 1 │ Mouse │ 25 │ Alice │ New … │
│ 2 │ 103 │ 3 │ Keyb… │ 75 │ Bob │ Lond… │
└───────┴────────┴─────┴───────┴───────┴───────┴───────┘

Here's an analysis:

- Primary Key: (OrderID, ProductID)
- Non-key attributes: Quantity, ProductName, ProductPrice,
  CustomerName, CustomerCity

Let's look at the functional dependencies:

- (OrderID, ProductID) -> Quantity (The quantity depends
  on a specific product in a specific order). This is a
  full dependency on the PK.
- Partial Dependency 1: ProductID -> ProductName (Knowing
  only the ProductID is enough to know the ProductName,
  regardless of the OrderID).
- Partial Dependency 2: ProductID -> ProductPrice (Knowing
  only the ProductID is enough to know the ProductPrice).
- Partial Dependency 3: OrderID -> CustomerName (Knowing
  only the OrderID is enough to know the CustomerName).
- Partial Dependency 4: OrderID -> CustomerCity (Knowing
  only the OrderID is enough to know the CustomerCity).

The attributes ProductName, ProductPrice, CustomerName, and
CustomerCity are non-key attributes that are dependent on
only part of the composite primary key (ProductID or
OrderID). This is a violation of 2NF because of these
partial dependencies.

Problems with Partial Dependencies (violating 2NF):

- Data Redundancy: ProductName and ProductPrice are
  repeated for every item from the same product.
  CustomerName and CustomerCity are repeated for every
  item in the same order. This wastes space.
- Update Anomalies: If Alice's city changes, you have to
  update it in multiple rows. If you miss one, you have
  inconsistent data.
- Deletion Anomalies: If OrderID 2 (Bob's keyboard) is
  deleted, we lose all information about Keyboard as a
  product (its name and price), unless it exists in
  another order.

`Checkpoint 1.5`: Third Normal Form (3NF)!

What is a Transitive Dependency?

This subtle issue is called a Transitive Dependency:

- A functional dependency A -> C is transitive if there's
  an attribute B such that:
  1.  A -> B (A determines B)
  2.  B -> C (B determines C)
  3.  B is not the Primary Key (or a candidate key -
      we'll get to that later)
  4.  C is not A (and B is not A or C)

- In simpler terms: A non-key attribute (C) is dependent
  on another non-key attribute (B), and that non-key
  attribute (B) is in turn dependent on the Primary Key
  (A).
  So, it's a dependency that goes through an intermediate
  non-key attribute: Primary Key -> Non-Key Attribute 1 ->
  Non-Key Attribute 2.

What is Third Normal Form (3NF)?

A table is in Third Normal Form (3NF) if and only if it
satisfies both of the following conditions:

1.  It is already in Second Normal Form (2NF).
2.  There are no transitive dependencies of non-key
    attributes on the primary key. (i.e., no non-key
    attribute is dependent on another non-key attribute).

Example: Violating 3NF (Transitive Dependency)

Let's consider a Courses table that is already in 2NF:

┌──────────┬───────────┬─────────┬───────────┬───────────┐
│ CourseI… │ CourseNa… │ Instru… │ Instruct… │ Instruct… │
├──────────┼───────────┼─────────┼───────────┼───────────┤
│ CS101 │ Intro to… │ 501 │ Prof. Sm… │ Computer… │
│ MA201 │ Calculus… │ 602 │ Prof. Jo… │ Mathemat… │
│ CS205 │ Data Str… │ 501 │ Prof. Sm… │ Computer… │
└──────────┴───────────┴─────────┴───────────┴───────────┘

Here's an analysis:

- Primary Key: CourseID
- Non-key attributes: CourseName, InstructorID,
  InstructorName, InstructorDept

Let's look at the functional dependencies:

- CourseID -> CourseName
- CourseID -> InstructorID
- InstructorID -> InstructorName (If you know the
  InstructorID, you know their Name)
- InstructorID -> InstructorDept (If you know the
  InstructorID, you know their Department)

Violation:
InstructorDept is transitively dependent on CourseID via
InstructorID.

- CourseID -> InstructorID
- InstructorID -> InstructorDept
  This means InstructorDept is dependent on InstructorID,
  which is a non-key attribute. This is a transitive
  dependency, violating 3NF.

Problems with Transitive Dependencies (violating 3NF):

- Data Redundancy: The InstructorName and InstructorDept
  are repeated for every course taught by that instructor.
- Update Anomalies: If Prof. Smith changes departments,
  you would have to update it in multiple rows for every
  course they teach. If you miss one, you have
  inconsistent data.
- Deletion Anomalies: If course MA201 is deleted, and
  Prof. Jones only teaches MA201, we would lose all
  information about Prof. Jones (their name and
  department).

In our next step, we'll discuss how to resolve these 3NF
violations.

Before we move to your practice exercise, let me show you
how to fix the example table that violated 3NF, just so you
have it for reference:

Original Table (Not 3NF - from our lesson):

┌──────────┬───────────┬─────────┬───────────┬───────────┐
│ CourseI… │ CourseNa… │ Instru… │ Instruct… │ Instruct… │
├──────────┼───────────┼─────────┼───────────┼───────────┤
│ CS101 │ Intro to… │ 501 │ Prof. Sm… │ Computer… │
│ MA201 │ Calculus… │ 602 │ Prof. Jo… │ Mathemat… │
│ CS205 │ Data Str… │ 501 │ Prof. Sm… │ Computer… │
└──────────┴───────────┴─────────┴───────────┴───────────┘

Decomposed into 3NF:

Table 1: Courses

- Primary Key: CourseID
- Attributes: CourseID, CourseName, InstructorID

1 CourseID (PK) | CourseName | InstructorID
2 --------------|-----------------|--------------
3 CS101 | Intro to CS | 501
4 MA201 | Calculus I | 602
5 CS205 | Data Structures | 501
(Here, InstructorID is a Foreign Key, which we'll cover
later, linking to the Instructors table.)

Table 2: Instructors

- Primary Key: InstructorID
- Attributes: InstructorID, InstructorName, InstructorDept

1 InstructorID (PK) | InstructorName | InstructorDept
2 ------------------|----------------|----------------
3 501 | Prof. Smith | Computer Sci
4 602 | Prof. Jones | Mathematics

Now, it's your turn to practice!

Consider a Projects table (which is in 2NF, but violates
3NF):

┌──────────┬─────────┬──────────┬───────────┬────────────┐
│ Project… │ Projec… │ Project… │ ProjectL… │ ProjectLe… │
├──────────┼─────────┼──────────┼───────────┼────────────┤
│ P1 │ Alpha │ L1 │ Sarah │ sarah@ex.… │
│ P2 │ Beta │ L2 │ Mike │ mike@ex.c… │
│ P3 │ Gamma │ L1 │ Sarah │ sarah@ex.… │
└──────────┴─────────┴──────────┴───────────┴────────────┘

This table violates 3NF. Decompose it into multiple tables
that satisfy 3NF. For each new table, clearly identify its
Primary Key.

`Checkpoint 1.6`: Boyce-Codd Normal Form (BCNF)!

What is a Candidate Key?

Before we dive into BCNF, let's clarify Candidate Keys. You
already know about Primary Keys, which are chosen to
uniquely identify rows.

- A Candidate Key is any attribute (column) or set of
  attributes that can potentially serve as the primary
  key.
- Characteristics of a Candidate Key:
  1.  Unique: Its values must uniquely identify each row.
  2.  Irreducible (Minimal): You can't remove any
      attribute from the set and still maintain
      uniqueness.
  3.  Non-NULL: It cannot contain null values.

- Every table has at least one candidate key. The one we
  choose as the main identifier is the Primary Key. Any
  other candidate keys are called Alternate Keys.

What is Boyce-Codd Normal Form (BCNF)?

A table is in Boyce-Codd Normal Form (BCNF) if:

1.  It is already in Third Normal Form (3NF).
2.  For every non-trivial functional dependency X -> Y, X
    must be a superkey.

Let's break down "superkey":

- A superkey is any set of attributes that includes a
  candidate key. So, if X is a superkey, it means X is
  either a candidate key itself or contains a candidate
  key.

Key Distinction: BCNF vs. 3NF

- 3NF focuses on transitive dependencies: It ensures that
  non-key attributes don't depend on other non-key
  attributes (PK -> NonKey1 -> NonKey2).
- BCNF is stricter: It essentially says that any attribute
  (or set of attributes) that determines another attribute
  must itself be a candidate key for the entire table.

The main difference emerges in specific situations,
primarily when:

- The table has multiple overlapping candidate keys.
- And one candidate key is dependent on another attribute
  that is not a candidate key.

Example: Violating BCNF (but in 3NF)

This is a subtle scenario. Consider a Student_Advisor
table:

┌────────────────┬───────────┬───────────┐
│ StudentID (PK) │ AdvisorID │ Subject │
├────────────────┼───────────┼───────────┤
│ S1 │ A1 │ Physics │
│ S2 │ A2 │ Chemistry │
│ S3 │ A1 │ Physics │
│ S4 │ A3 │ Math │
└────────────────┴───────────┴───────────┘

And assume these business rules:

1.  (StudentID, Subject) is a candidate key (a student can
    have only one advisor for a given subject).
2.  AdvisorID -> Subject (Each advisor is an expert in, and
    advises on, only one specific subject).

Let's analyze:

- Primary Key: Let's choose StudentID as the PK for now
  (though we'll see why this is problematic). For
  simplicity, let's say a student can only have one
  advisor, so StudentID is a PK.
- Candidate Keys: StudentID is a candidate key.
  (StudentID, Subject) is also a candidate key as per rule
  1.
- Functional Dependencies:
  - StudentID -> AdvisorID (Each student has one
    advisor).
  - StudentID -> Subject (Each student has one main
    subject).
  - AdvisorID -> Subject (An advisor specializes in one
    subject).

Is it in 1NF? Yes.
Is it in 2NF? Yes (no composite PK, so no partial
dependencies).
Is it in 3NF? Yes, there are no transitive dependencies (PK
-> NonKey1 -> NonKey2).

Why it violates BCNF:
We have the functional dependency AdvisorID -> Subject.

- The determinant is AdvisorID.
- Is AdvisorID a superkey (i.e., a candidate key or
  contains one)? No, AdvisorID by itself is not unique for
  the table (e.g., A1 advises S1 and S3).
- Since the determinant (AdvisorID) is not a superkey,
  this table violates BCNF.

Problems with this BCNF violation:

- Data Redundancy: If an advisor advises multiple
  students, their subject is repeated for each student.
- Update Anomalies: If Advisor A1's subject changes, you
  have to update every row where A1 appears.
- Deletion Anomalies: If student S4 is removed (and S4 is
  the only student advised by A3), we lose the information
  that A3 advises on Math.

Original Table (Violating BCNF - from our lesson):
Student_Courses_Instructors (Primary Key: (StudentID,
CourseName))

- Functional Dependency: InstructorName -> CourseName

┌───────────────────┬────────────────────┬───────────────┐
│ StudentID (PK pa… │ CourseName (PK pa… │ InstructorNa… │
├───────────────────┼────────────────────┼───────────────┤
│ S1 │ DB Fundamentals │ Prof. Smith │
│ S1 │ Algorithms │ Prof. Jones │
│ S2 │ DB Fundamentals │ Prof. Smith │
│ S3 │ DB Fundamentals │ Prof. White │
└───────────────────┴────────────────────┴───────────────┘

Decomposition to BCNF:

Table 1: Student_Enrollment

- Primary Key: (StudentID, InstructorName)

1 StudentID (PK) | InstructorName (PK)
2 ---------------|--------------------
3 S1 | Prof. Smith
4 S1 | Prof. Jones
5 S2 | Prof. Smith
6 S3 | Prof. White

Table 2: Instructor_Courses

- Primary Key: InstructorName
- Attributes: InstructorName, CourseName

1 InstructorName (PK) | CourseName
2 --------------------|---------------
3 Prof. Smith | DB Fundamentals
4 Prof. Jones | Algorithms
5 Prof. White | DB Fundamentals

---

Now, it's your turn to apply this knowledge! We'll use the
same example to help you focus on the decomposition.
