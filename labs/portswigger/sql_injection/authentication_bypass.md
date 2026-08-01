## Lab 2 — Authentication Bypass via SQL Injection

### Goal

Log in to the application as the administrator user.

### First Attempt

I tried placing the SQL injection in the password field:

**Username:**
```text
administrator
````

**Password:**

```text
' OR 1=1--
```

I received:

```text
Invalid CSRF token (session does not contain a CSRF token)
```

### Successful Payload

I moved the SQL injection to the username field:

```text
administrator' OR 1=1--
```

I used random characters as the password.

### Why It Worked

The username input was included in the SQL query used for authentication.

The injected `OR 1=1` changed the logic of the query, while `--` commented out the remaining part of the query, including the original password check.

This allowed the authentication query to succeed without knowing the administrator's password.

### What I Learned

* SQL injection can be used to bypass authentication.
* The location of the injection matters.
* Different input parameters may affect different parts of an SQL query.
* An SQL injection payload needs to fit the structure of the original query.
* The injection has to be placed in a parameter that reaches the relevant SQL query in a way that lets the injected logic affect authentication.

