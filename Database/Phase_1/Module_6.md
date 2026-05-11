`Checkpoint 6.1`:E-commerce Schema.

TEACH: The Blueprint (Entities & Relationships)

The Analogy
Imagine a physical department store.

- The Entities are the "nouns" of the business: The Customers walking in, the Products on the shelves, and the Orders (receipts)
  printed at the register.
- The Relationships are the "verbs": A customer places an order; an order contains products.

The Strategy
To build a solid database, we start with these core tables:

1.  users: To store people's info (name, email).
2.  products: To store what we're selling (name, price, stock).
3.  orders: To record that a sale happened (who bought it and when).
4.  order_items: This is the "Bridge Table." Since one order can have many products, and one product can be in many orders, we need
    a special table to link them.

The Connections (Relationships)

- Users to Orders: 1-to-Many (One user can have many orders).
- Orders to Products: Many-to-Many (One order has many products, and one product appears in many orders).

Summary
We are designing 4 tables that link together to track a complete shopping experience.
