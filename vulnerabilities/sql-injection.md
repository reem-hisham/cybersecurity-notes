# SQL Injection

## Definition

**SQL Injection (SQLi)** is a vulnerability that occurs when untrusted user input is incorporated into an SQL query without proper handling.

An attacker may be able to manipulate the query and make the database perform unintended operations.

---

## How It Works

A vulnerable application may construct a query using user input:

```text
User Input
    ↓
Application
    ↓
SQL Query
    ↓
Database
```

If the input is not properly handled, the attacker may be able to modify the intended SQL query.

---

## Common SQL Injection Types

### 1. In-Band / Visible SQLi

The application directly reveals useful database information.

Common technique:

* `UNION SELECT`

Can be used to:

* Determine the number of columns.
* Retrieve data from other tables.
* Extract usernames and passwords.

---

### 2. Authentication Bypass

SQL injection can sometimes manipulate a login query so that authentication conditions are bypassed.

The result depends on how the application's SQL query is constructed.

---

### 3. Blind SQL Injection

The application does not directly return the result of the injected SQL.

Instead, information must be inferred through another observable behavior.

Common approaches:

* Conditional responses
* Conditional errors
* Time delays
* Out-of-band interactions

---

### 4. Error-Based SQLi

The database produces an error that reveals information or can be used as a signal.

Conceptually:

```text
SQL Injection
      ↓
Database error
      ↓
Observable difference
      ↓
Information can be inferred
```

---

### 5. Time-Based SQLi

The attacker causes the database to delay its response under a specific condition.

```text
Condition TRUE
      ↓
Delay
      ↓
Longer response

Condition FALSE
      ↓
No delay
      ↓
Normal response
```

This can be used to infer information one condition at a time.

---

### 6. Out-of-Band SQLi

The database is induced to interact with an external system controlled or monitored by the attacker.

```text
SQL Injection
      ↓
Database
      ↓
External interaction
      ↓
Attacker observes interaction
```

OOB can be used to:

* Confirm SQL execution.
* Exfiltrate data.

The exact technique depends heavily on the database system.

---

## Impact

Depending on the vulnerability and database privileges, SQL Injection may allow an attacker to:

* Read sensitive database information.
* Retrieve credentials.
* Bypass authentication.
* Modify database records.
* Delete data.
* Potentially perform further actions through database functionality.

The actual impact depends on the application's database permissions and configuration.

---

## Important Concepts

### UNION

`UNION` can combine the results of multiple `SELECT` statements.

Useful for retrieving data from other queries when the number and types of columns are compatible.

### `DUAL`

Oracle provides the `DUAL` table, which can be used when a `SELECT` statement needs a table reference.

### Blind SQLi

When the database result is not directly visible, use an observable side channel:

```text
Response
Error
Timing
OOB
```

### OOB Data Exfiltration

Instead of returning data through the application, sensitive information can be incorporated into an external request.

```text
Database data
      ↓
External request
      ↓
Attacker-controlled endpoint
```

---


### WAF / Filter Bypass

A SQL injection payload may be blocked by a WAF or input filter even when
the underlying parameter is vulnerable.

Possible approaches depend on the context and filtering mechanism.

In XML-based input, encoding SQL characters using XML entities can sometimes
bypass filters that detect the original representation of the payload.

Important:
A WAF blocking a payload does not necessarily mean the application is not
vulnerable.

### SQL Injection Through XML

XML input can contain user-controlled values that are later used in SQL
queries.

Therefore, XML parameters should also be considered when identifying
potential SQL injection points.

## Prevention

SQL Injection can be prevented primarily by:

* Using **parameterized queries / prepared statements**.
* Avoiding dynamic SQL construction with untrusted input.
* Applying appropriate input validation.
* Using least-privilege database accounts.
* Properly handling database errors.
* Avoiding unnecessary database functionality and privileges.

---

## Key Takeaways

* SQLi occurs when attacker-controlled input can alter an SQL query.
* SQLi can be visible or blind.
* Blind SQLi requires an observable side channel.
* Different DBMSs have different syntax and capabilities.
* `UNION`, errors, timing, and OOB interactions are different ways of obtaining information from a SQLi vulnerability.
* OOB SQLi uses an **external interaction as the information channel**.
* Understanding the underlying technique is more important than memorizing payloads.
