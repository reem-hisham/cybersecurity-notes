## Lab 7 — Blind SQL Injection: Extracting Data Through Errors

### Goal

Exploit a blind SQL injection vulnerability in the `TrackingId` cookie to
retrieve the administrator's password and log in as the administrator.

### What I Observed

The application used the `TrackingId` cookie inside an SQL query.

The query results were not displayed, but the application returned detailed
database errors.

This meant that SQL errors could be used to extract information from the
database.

### Step 1 — Identify the Injection Point

I found the `TrackingId` cookie in Burp Suite's HTTP History and sent the
request to Repeater.

I added a single quote:

```text
TrackingId=original_value'
````

The application returned an SQL error that revealed the structure of the
query.

This showed that my input was being placed inside a single-quoted SQL
string.

### Step 2 — Comment Out the Rest of the Query

I used:

```text
TrackingId=original_value'--
```

The `--` commented out the remaining part of the SQL query.

The error disappeared, confirming that the modified query was syntactically
valid.

### Step 3 — Test a Subquery

I tested whether I could execute a subquery:

```text
TrackingId=original_value' AND CAST((SELECT 1) AS int)--
```

This produced an error because the `AND` condition expected a Boolean
expression.

I changed it to:

```text
TrackingId=original_value' AND 1=CAST((SELECT 1) AS int)--
```

This removed the error, confirming that the injected condition was valid.

### Step 4 — Extract a Username

I changed the subquery to retrieve a username:

```text
TrackingId=' AND 1=CAST((SELECT username FROM users) AS int)--
```

The database returned an error because the query returned more than one row.

I therefore limited the subquery to one result:

```text
TrackingId=' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--
```

The error message revealed:

```text
administrator
```

This showed that the administrator was the first user returned by the query.

### Step 5 — Extract the Administrator Password

I changed the subquery from `username` to `password`:

```text
TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--
```

The database error leaked the administrator's password.

I then used the recovered credentials to log in as the administrator and
solve the lab.

### Why It Worked

The application did not display the results of my injected SQL query.

However, it displayed detailed database errors.

I used this behavior to deliberately cause a type-conversion error:

```sql
CAST(value AS int)
```

If the value could not be converted to an integer, the database included
the value in the error message.

For example:

```text
username = "administrator"
        ↓
CAST("administrator" AS int)
        ↓
conversion error
        ↓
"administrator" appears in the error
```

This allowed me to extract database values indirectly through error messages.

### What I Learned

* Detailed database errors can disclose sensitive information.
* SQL errors can sometimes be used as an information-leak channel.
* `CAST()` can be used to force a type-conversion error.
* A subquery can be used to retrieve values from another table.
* `LIMIT 1` can restrict a subquery to a single row.
* SQL injection can still extract data even when query results are not
  directly displayed.
* Burp Repeater is useful for manually developing and testing an injection
  step by step.
* Error messages can reveal both database information and application
  behavior.

