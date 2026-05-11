`Checkpoint 1.1` - How B-Trees Work.

TEACH: The Library Analogy
Imagine you are in a massive library with 1 million books.

- Without an Index (Sequential Scan): To find a book titled "Database Internals," you start at the first shelf and look at every single book one by one
  until you find it. In the worst case, you look at 1 million books. This is a Sequential Scan (or Table Scan).
- With a B-Tree Index: You go to the index card cabinet. You see drawers labeled A-G, H-N, O-Z. You pull the "D" drawer. Inside, you see tabs for
  "Da-De," "Df-Dj," etc. You quickly narrow it down to the exact shelf and position. You only looked at maybe 3 or 4 "levels" of organization to find
  your book.

The Concept: What is a B-Tree?
A B-Tree (Balanced Tree) is the default index type in PostgreSQL (and most SQL databases).

1.  Balanced: The distance from the root (the top) to any "leaf" (the bottom where the data pointers are) is always the same. This ensures consistent
    performance.
2.  Sorted: The data in the index is kept in a specific order.
3.  Logarithmic Search: Instead of checking N rows, you check roughly log(N) nodes. For 1 million rows, a B-Tree might only be 3 or 4 levels deep.

Example: Finding ID 42
If you have an ID column indexed with a B-Tree:

- The Root node might say: "Values 1-50 go left, 51-100 go right."
- You go left. The next node says: "1-25 go left, 26-50 go right."
- You go right. The next node points you directly to the physical location of row 42 on the disk.

Summary: B-Trees turn a "needle in a haystack" problem into a "follow the signs" problem.

`Checkpoint: 1.2` - Clustered vs Non-Clustered Indexes.

TEACH: The Dictionary vs. The Textbook

- Clustered Index (The Dictionary): Think of a physical dictionary. The words are the index, and the definitions are the data. Because the words are
  stored in alphabetical order, once you find the word "Database," the definition is right there. The index is the data.
- Non-Clustered Index (The Textbook Index): Think of the index at the back of a textbook. It says "Indexing... page 42." You find "Indexing" in the back
  (the index), but then you have to jump to page 42 (the actual table data) to read about it.

The Concept

1.  Clustered Index:
    - Determines the physical order of data on the disk.
    - Only one per table (you can't sort a physical pile of books two different ways at once).
    - Usually created automatically on your Primary Key.
2.  Non-Clustered Index:
    - A separate structure (a B-Tree) that contains a copy of the indexed column and a "pointer" (address) to where the full row lives.
    - You can have many of these.
    - Cost: Searching a non-clustered index takes an extra step: find the entry, then go fetch the row from the "Heap" (the main table).

Example (PostgreSQL Specific)
In PostgreSQL, all indexes are technically "Non-Clustered" (they point to a "Heap" file), but the Primary Key acts as the most important index. However,
you can use the CLUSTER command to physically reorder a table to match an index!

Summary:

- Clustered = Data is sorted by this. "I'm already here."
- Non-Clustered = Separate list with pointers. "I found the address, now I have to drive there."

`Checkpoint: 1.3` - When to Index (and When NOT to)

TEACH: The Index Card Analogy

- The Small Box: If you only have 10 index cards in a box, it's faster to just flip through all 10 cards (Sequential Scan) than it is to look at a "Table
  of Contents" (Index) first and then find the card.
- The Updating Nightmare: Imagine if every time you added a book to the library, you had to update 50 different index catalogs. You’d spend more time
  writing in catalogs than putting books on shelves!

The Concept: The Trade-off
Indexes are not free. They have two major costs:

1.  Write Performance: Every INSERT, UPDATE, or DELETE on a table requires the database to also update every single index on that table. If you have 10
    indexes, one "Write" becomes 11 writes.
2.  Storage: Indexes take up disk space and memory (RAM). Over-indexing can bloat your database.

When NOT to Index:

1.  Small Tables: If a table has only a few hundred rows, the database will often ignore the index and just do a "Seq Scan" because it's faster.
2.  Low Cardinality (Low Selectivity): If you index a column like is_active (which is only TRUE or FALSE), the index doesn't help much. It still has to
    look at half the table!
3.  Frequently Updated Columns: If a column changes every second, the index maintenance will slow down your application.

When TO Index:

1.  Columns in WHERE clauses: Things you search by frequently (email, username, order_id).
2.  Columns used for JOINs: To find matching rows in other tables quickly.
3.  Columns used for ORDER BY: Since B-Trees are sorted, they make sorting results almost "free."

Summary: Index only what you search/join/sort by, and avoid indexing everything "just in case."
