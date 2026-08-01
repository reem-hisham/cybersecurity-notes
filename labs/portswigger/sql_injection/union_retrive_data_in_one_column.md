## Lab 4 — SQL Injection UNION Attack: Retrieving Multiple Values in a Single Column

### Goal

Use a UNION attack to retrieve all usernames and passwords from the `users`
table, then use the administrator credentials to log in.

### What I Did

- Determined that the original query returned two columns.
- Determined that only one of the two columns could contain text data.
- Since I needed to retrieve both the username and password, I combined them
  into a single column.
- Used the `users` table to retrieve the credentials.
- Used the retrieved administrator credentials to log in.

### Determining the Columns

I verified that the query returned two columns and that only the second
column could contain text:

```text
'+UNION+SELECT+NULL,'abc'--
````

`NULL` was used for the first column because it did not need to contain
text.

### Retrieving Multiple Values

The `users` table contained:

```text
username
password
```

Since only one column could display text, I combined the username and
password into a single value using:

```sql
username || '~' || password
```

The successful payload was:

```text
'+UNION+SELECT+NULL,username||'~'||password+FROM+users--
```

The `~` was used as a separator between the username and password.

### Why It Worked

The UNION query had to return the same number of columns as the original
query.

The original query returned two columns, so the injected query also returned
two:

```sql
SELECT NULL, username || '~' || password FROM users
```

Only the second column could display text, so the username and password were
combined into that single column.

The `||` operator concatenates strings in Oracle.

For example, the result could look like:

```text
administrator~password123
```

This allowed both values to be retrieved even though only one column could
display text.

### What I Learned

* A UNION attack can retrieve data from another table.
* The number of columns in the injected query must match the original query.
* The columns do not necessarily all have to contain text.
* `NULL` can be used for columns that do not need to contain data.
* Multiple values can be combined into a single column using string
  concatenation.
* In Oracle, `||` is used to concatenate strings.
* A separator such as `~` makes concatenated values easier to distinguish.
* I can use information retrieved through SQL injection to understand the
  database structure and potentially access sensitive data.

