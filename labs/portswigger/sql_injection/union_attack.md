## Lab 3 — SQL Injection UNION Attack

### Goal

Use a UNION attack to retrieve and display the database version string.

The database was Oracle.

### What I Did

- Identified the category parameter as the injection point.
- Determined that the original query returned two columns.
- Determined that both columns could contain text data.
- Used a UNION SELECT statement to inject my own query.
- Learned that Oracle requires a `FROM` clause in SELECT statements.
- Used Oracle's built-in `dual` table to satisfy the `FROM` requirement.
- Retrieved the database version from the `v$version` table.

### First Step — Determine the Number of Columns

I needed to determine how many columns the original query returned before using a UNION attack.

I verified that the query returned two columns using:

```text
'+UNION+SELECT+'abc','def'+FROM+dual--
````

The response confirmed that the UNION query was compatible with the original query.

### Successful Payload

```text
'+UNION+SELECT+BANNER,+NULL+FROM+v$version--
```

This retrieved the database version string from Oracle's `v$version` table.

### Why It Worked

A UNION attack combines the results of the original query with the results
of an injected SELECT statement.

For a UNION attack to work, the injected SELECT needs to return the same
number of columns as the original query.

The original query returned two columns, so the injected query also returned
two columns:

```sql
SELECT BANNER, NULL FROM v$version
```

`BANNER` contained the database version information, while `NULL` was used
for the second column.

Oracle requires SELECT statements to specify a table in the `FROM` clause.
The built-in `dual` table can be used when a table is otherwise not needed.

### What I Learned

* UNION can be used to combine the results of an injected query with the
  original query.
* I need to determine the number of columns returned by the original query
  before performing a UNION attack.
* The number of columns in the UNION SELECT must match the original query.
* I need to determine which columns can contain text data.
* Oracle has a built-in `dual` table that can be used in SELECT statements.
* Oracle's `v$version` can contain database version information.
* `NULL` can be useful for filling an unused column in a UNION query.

## Notes

* For UNION SQLi, first figure out the column count, then which columns can hold the data I want.

* for MYSQL ,Microsoft to get the version ot the database use `SELECT @@version` instead, so the payload for them is :
  ```text
  'UNION+SELECT+@@VERSION,NULL--+
  ``` 
  - Important note :  MYSQL needs a space after the `--` so we put `+`. 
