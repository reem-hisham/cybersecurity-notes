# SQL for Web Security

## Core Flow

```text
Application
   ↓
SQL Query
   ↓
Database
   ↓
Result
   ↓
Application
   ↓
HTTP Response
````

SQL is the language applications use to interact with relational databases.

---

## SELECT

Read data:

```sql
SELECT username, email
FROM users;
```

All columns:

```sql
SELECT *
FROM users;
```

With a condition:

```sql
SELECT *
FROM users
WHERE id = 5;
```

---

## WHERE

Filters rows:

```sql
SELECT *
FROM users
WHERE role = 'admin';
```

Can use:

```sql
AND
OR
>
<
>=
<=
=
```

Security question:

> Is user-controlled input being used inside the WHERE condition?

---

## ORDER BY

Sort results:

```sql
SELECT *
FROM products
ORDER BY price ASC;
```

```sql
SELECT *
FROM products
ORDER BY price DESC;
```

---

## GROUP BY

Groups rows:

```sql
SELECT user_id, COUNT(*)
FROM orders
GROUP BY user_id;
```

Useful for aggregation such as:

```text
COUNT()
SUM()
AVG()
MAX()
MIN()
```

---

## JOIN

Combines related tables:

```sql
SELECT users.username, orders.product
FROM users
JOIN orders
ON users.id = orders.user_id;
```

Think:

```text
users.id
   ↕
orders.user_id
```

---

## UNION

Combines results from multiple SELECT statements:

```sql
SELECT username FROM users
UNION
SELECT email FROM users;
```

The SELECT statements must have compatible column counts/types.

Important for understanding UNION-based SQL Injection.

---

## INSERT

Add data:

```sql
INSERT INTO users (username, email)
VALUES ('alice', 'alice@example.com');
```

---

## UPDATE

Modify existing data:

```sql
UPDATE users
SET email = 'new@example.com'
WHERE id = 5;
```

Always understand the effect of the `WHERE` condition.

---

## DELETE

Remove data:

```sql
DELETE FROM users
WHERE id = 5;
```

Without `WHERE`, the operation can affect all rows.

---

## SQL Security Mental Model

```text
User Input
    ↓
HTTP Request
    ↓
Backend
    ↓
SQL Query
    ↓
Database
    ↓
Result
    ↓
HTTP Response
```

When analyzing a web application, ask:

1. Where does my input go?
2. Does it reach a SQL query?
3. How is it used?
4. What tables/columns are involved?
5. What does changing the input change?
6. What data can the application access?

The goal is not to memorize SQL syntax.

The goal is to understand:

> **How application logic becomes a database query, and how that query produces the response I see.**
