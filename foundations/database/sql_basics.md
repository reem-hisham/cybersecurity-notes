# SQL Basics

## What Is SQL?

**SQL (Structured Query Language)** is used to interact with relational
databases.

A database stores information in tables made up of:

```text
Database
   ↓
Tables
   ↓
Rows + Columns
```

Example:

```text
users
--------------------------------
id | username | password
--------------------------------
1  | admin    | secret123
2  | john     | password1
```

## Basic Query Structure

```sql
SELECT column
FROM table
WHERE condition;
```

Example:

```sql
SELECT password
FROM users
WHERE username = 'administrator';
```

This retrieves the password for the user named `administrator`.

## Important SQL Keywords

### SELECT

Used to retrieve data.

```sql
SELECT username FROM users;
```

### FROM

Specifies the table to query.

```sql
SELECT username
FROM users;
```

### WHERE

Filters the returned rows.

```sql
SELECT username
FROM users
WHERE id = 1;
```

### UNION

Combines the results of multiple `SELECT` statements.

```sql
SELECT column1 FROM table1
UNION
SELECT column2 FROM table2;
```

For `UNION` to work correctly, the queries generally need compatible
numbers and types of columns.

## Oracle `DUAL`

Oracle provides a special table called `DUAL`.

It can be used when a `SELECT` statement does not need to retrieve data from
a normal table.

```sql
SELECT 'test' FROM dual;
```

## SQL Comments

Comments can cause the rest of a query to be ignored.

A commonly encountered SQL comment syntax is:

```sql
--
```

The exact comment syntax depends on the database system.

## Important Concept

SQL is the language used to communicate with the database.

SQL Injection occurs when attacker-controlled input is able to alter the
intended SQL query.

Therefore, understanding basic SQL syntax is important before studying
SQL Injection.

## Key Takeaways

* SQL is used to interact with relational databases.
* `SELECT` retrieves data.
* `FROM` specifies the table.
* `WHERE` filters results.
* `UNION` combines query results.
* Oracle provides the `DUAL` table.
* SQL syntax and features can differ between database systems.
