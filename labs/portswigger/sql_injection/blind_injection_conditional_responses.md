## Lab 5 — Blind SQL Injection with Conditional Responses

### Goal

Exploit a blind SQL injection vulnerability to determine the administrator's
password and then log in as the administrator.

The application did not return the SQL query results or display SQL errors,
but it showed a `Welcome back` message when the SQL query returned at least
one row.

### What I Observed

The application used a tracking cookie in a SQL query.

The important behavior was:

- SQL query returns a row → `Welcome back` message appears.
- SQL query returns no rows → `Welcome back` message does not appear.
- SQL errors were not displayed.

This allowed me to use the application's response as a **true/false
indicator**.

### Step 1 — Determine the Password Length

I tested different password lengths using:

```text
' AND (SELECT LENGTH(password) FROM users WHERE username = 'administrator') = 20--
````

I used Burp Suite Intruder to test different values for the password length.

When the condition was correct, the application displayed:

```text
Welcome back
```

The correct password length was:

```text
20 characters
```

### Step 2 — Extract the Password Character by Character

After determining the password length, I tested each character position
individually.

I used:

```text
'AND SUBSTR((SELECT password FROM users WHERE username = 'administrator'),position,1)='a
```

I changed the position and tested different lowercase letters and numbers
using Burp Suite Intruder.

For example:

```text
Position 1 → test possible characters
Position 2 → test possible characters
Position 3 → test possible characters
...
Position 20 → test possible characters
```

When the tested character was correct, the application returned the
`Welcome back` message.

I repeated this process until I recovered all 20 characters of the password.

### Why It Worked

This was a **blind SQL injection** because the application did not directly
return the results of the SQL query.

Instead, I used the application's behavior as a source of information.

The injected condition was either true or false:

```text
Condition is TRUE
        ↓
SQL query returns a row
        ↓
"Welcome back" appears
```

or:

```text
Condition is FALSE
        ↓
SQL query returns no rows
        ↓
"Welcome back" does not appear
```

I first used this behavior to determine the password length.

Then I used `SUBSTR()` to test individual characters of the password.

### What I Learned

* Blind SQL injection can be exploited even when query results are not
  directly displayed.
* Application behavior can act as a true/false signal.
* I can determine the length of a value using `LENGTH()`.
* I can extract individual characters using `SUBSTR()`.
* The same true/false technique can be repeated to recover an entire value
  character by character.
* Burp Suite Intruder can automate testing many possible values.
* Blind SQL injection can require significantly more requests than
  in-band SQL injection because information must be inferred indirectly.

