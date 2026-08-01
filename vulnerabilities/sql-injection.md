
# SQL Injection

## What Is SQL Injection?

SQL Injection (SQLi) is a web security vulnerability that occurs when
untrusted user input is included directly in an SQL query.

An attacker may be able to manipulate the SQL query by providing
specially crafted input.

## Why Does SQL Injection Happen?

SQL injection happens when an application builds SQL queries using
untrusted input without safely separating data from SQL code.

### Vulnerable Example

```sql
SELECT * FROM users WHERE username = 'USER_INPUT';
````

If `USER_INPUT` is directly controlled by the user, the SQL query
may be manipulated.

## What Can SQL Injection Do?

Depending on the application and database, SQL injection may allow an
attacker to:

* Bypass authentication
* Read data from the database
* Modify data
* Delete data
* Access information belonging to other users
* Perform other database operations

## Types of SQL Injection

* In-band SQL Injection
* UNION-based SQL Injection
* Blind SQL Injection
* Boolean-based Blind SQL Injection
* Time-based Blind SQL Injection
* Out-of-band SQL Injection

## SQL Injection in Web Applications

SQL injection can occur when user-controlled input is included in
parts of an SQL query such as:

* `WHERE` clauses
* `ORDER BY` clauses
* `INSERT` statements
* `UPDATE` statements
* `DELETE` statements

## Key Concepts I Learned

### Query Manipulation

SQL injection can change the logic of an existing SQL query instead of
simply providing normal data.

### Authentication Bypass

SQL injection can sometimes manipulate an authentication query so that
the application accepts a login without the correct password.

### UNION Attacks

A UNION attack can combine the results of an injected query with the
original query and retrieve data from other database tables.

The number of columns returned by the injected query must match the
original query.

### Blind SQL Injection

Blind SQL injection occurs when the application does not directly return
the results of the injected SQL query.

Information can instead be extracted by observing differences in the
application's responses or errors.

### Database-Specific Techniques

SQL injection techniques can differ between database systems.

For example, Oracle uses the `DUAL` table for certain `SELECT` statements,
and provides database-specific functions and tables such as `v$version`.

## Things I Need to Learn

* How to identify SQL injection (✅)
* How SQL queries are manipulated (✅)
* How authentication bypass works (✅)
* How UNION-based SQL injection works (✅)
* How to determine the number of columns (✅)
* How to determine which columns contain useful data (✅)
* How to retrieve multiple values in a single column (✅)
* How blind SQL injection works (✅)
* How blind SQL injection with conditional responses works (✅)
* How blind SQL injection with conditional errors works (✅)
* How time-based SQL injection works
* How SQL injection can be prevented

## Labs

My practical SQL injection experience is documented in:

* `labs/portswigger/sql-injection/`

