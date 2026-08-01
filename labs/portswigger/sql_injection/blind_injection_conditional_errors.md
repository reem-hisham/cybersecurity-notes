## Lab 6 — Blind SQL Injection with Conditional Errors

### Goal

Exploit a blind SQL injection vulnerability to determine the administrator's
password and then log in as the administrator.

The application did not return SQL query results and did not respond
differently based on whether the query returned rows.

However, when the SQL query caused an error, the application returned a
custom error message.

### What I Observed

The application gave me no direct information about the SQL query result.

Instead, I could use a SQL error as a signal:

- Condition is TRUE → deliberately cause an error → custom error message
  appears.
- Condition is FALSE → no error → normal response.

This allowed me to turn a hidden SQL condition into something observable.

### Step 1 — Determine the Password Length

I used an Oracle `CASE` expression to test whether the administrator's
password had a specific length.

My payload was:

```text
'AND 1 = (SELECT CASE WHEN (SELECT LENGTH(password) FROM users WHERE username = 'administrator') = 20 THEN TO_NUMBER(1/0) ELSE 1 END FROM DUAL)--
````

The important logic was:

```sql
CASE
    WHEN password_length = 20
    THEN TO_NUMBER(1/0)
    ELSE 1
END
```

`TO_NUMBER(1/0)` causes an error.

When the tested length was correct, the error occurred and the application
returned its custom error message.

This allowed me to determine that the password was 20 characters long.

### Step 2 — Extract the Password Character by Character

After determining the length, I tested each character individually.

I used a payload based on:

```text
'AND 1 = (SELECT CASE WHEN (SUBSTR((SELECT password FROM users WHERE username = 'administrator'),position,1) = 'A') THEN TO_NUMBER(1/0) ELSE 1 END FROM DUAL)--
```

I changed:

* `position` to test each character position.
* `'A'` to test different possible characters.

When the tested character was correct, the condition became true and
`TO_NUMBER(1/0)` caused an error.

The custom error response therefore told me that the character was correct.

I repeated this process until I recovered the complete password.

### Why It Worked

This was a blind SQL injection because the application did not directly
return the query results.

Unlike the previous blind SQLi lab, the application did not provide a
different response based on whether the query returned rows.

Instead, I deliberately created an error when a condition was true.

The basic logic was:

```text
Condition TRUE
      ↓
CASE executes TO_NUMBER(1/0)
      ↓
SQL error
      ↓
Custom error message
```

While:

```text
Condition FALSE
      ↓
CASE returns 1
      ↓
No SQL error
      ↓
Normal response
```

This gave me a true/false signal that I could use to extract the password.

### Oracle-Specific Techniques

This lab uses Oracle, so I learned about:

* `DUAL` — Oracle's built-in table used in the `FROM` clause.
* `CASE WHEN` — used to perform conditional logic.
* `TO_NUMBER(1/0)` — used here to deliberately trigger an error.


### What I Learned

* Blind SQL injection can use errors as a source of information.
* A deliberately triggered error can act as a true/false signal.
* `CASE WHEN` can be used to conditionally trigger an SQL error.
* Oracle provides `DUAL` for SELECT statements that do not need a normal
  table.
* Conditional errors are another technique for extracting information when
  normal query results and conditional responses are unavailable.
* Burp Suite can automate testing different positions and characters.
