# SQL Injection Methodology

## 1. Identify the Injection Point

Look for user-controlled input that may be included in a database query.

Common locations:

* URL parameters
* Form parameters
* Cookies
* HTTP headers
* JSON/XML parameters

Example:

```text
TrackingId
category
id
username
```

---

## 2. Test for SQL Injection

Start by checking whether modifying the input changes the application's behavior.

Consider:

* Quotes
* SQL syntax
* Boolean conditions
* Errors
* Response differences

The goal is to determine:

> **Is my input being interpreted as part of an SQL query?**

---

## 3. Determine the SQLi Type

After identifying a potential SQL injection, determine which technique is useful.

```text
Can I directly see database data?
        ↓
   Yes → UNION / visible SQLi

        ↓ No

Can I distinguish TRUE/FALSE?
        ↓
   Yes → Conditional / Blind SQLi

        ↓ No

Can I trigger a database error?
        ↓
   Yes → Error-based SQLi

        ↓ No

Can I observe a response delay?
        ↓
   Yes → Time-based SQLi

        ↓ No

Can the database interact externally?
        ↓
   Yes → OOB SQLi
```

---

## 4. Determine the Database

Identify the DBMS when possible.

Different databases have different:

* Syntax
* System tables
* Functions
* Error behavior
* OOB capabilities

Examples encountered during labs:

```text
Oracle
PostgreSQL
```

Do not assume that a payload that works on one DBMS will work on another.

---

## 5. Extract Information

Once SQL injection is confirmed, determine what information can be retrieved.

Possible targets:

```text
Database
    ↓
Tables
    ↓
Columns
    ↓
Users
    ↓
Passwords / sensitive data
```

For UNION-based SQLi, first determine:

1. Number of columns.
2. Which columns accept useful data.
3. Which database is being used.
4. Which tables/columns contain useful information.

---

## 6. Blind SQL Injection

When the application does not directly return database results, use an observable side channel.

Possible channels:

```text
Conditional response
        ↓
Conditional error
        ↓
Response timing
        ↓
Out-of-band interaction
```

The goal is to turn an invisible database result into something observable.

---

## 7. Out-of-Band SQL Injection

Use OOB when the application does not provide a useful response, error, or timing signal and the database can perform external interactions.

General approach:

```text
SQL Injection
      ↓
Database executes injected SQL
      ↓
Database performs external interaction
      ↓
Burp Collaborator / external server
      ↓
Observe the interaction
```

OOB can be used for:

* Confirming SQL execution
* Data exfiltration

The exact technique depends heavily on the DBMS.

---

## 8. Choose the Simplest Reliable Technique

Do not automatically use the most advanced technique.

Prefer:

```text
Visible result
    ↓
Conditional response
    ↓
Error-based
    ↓
Time-based
    ↓
OOB
```

Use the technique that provides the clearest and most reliable signal for the situation.

---

## 9. Document the Vulnerability

For each SQLi vulnerability, record:

* Injection point
* SQLi type
* DBMS
* Observable behavior
* Technique used
* Successful payload
* What the payload accomplished
* Limitations
* What was learned

Keep detailed payloads and lab-specific steps in the corresponding **lab/writeup**, rather than putting every payload into this methodology.

---

# SQLi Decision Flow

```text
Find user-controlled input
          ↓
Test for SQL injection
          ↓
Confirm vulnerability
          ↓
Identify DBMS
          ↓
Can I see the result?
     ┌────┴────┐
    YES        NO
     ↓          ↓
  UNION      Find a side channel
               ↓
       ┌───────┼────────┐
       ↓       ↓        ↓
   Response   Error    Timing
                         ↓
                        OOB
                         ↓
                External interaction
                         ↓
                    Data extraction
```

# Key Principles

* Understand the application's behavior before choosing a technique.
* Identify the DBMS before relying on database-specific syntax.
* Use the simplest reliable SQLi technique available.
* Blind SQLi requires an observable side channel.
* OOB SQLi uses an external interaction as the information channel.
* Payloads are tools; understanding the underlying mechanism is more important than memorizing them.
* Keep methodology reusable and put lab-specific details in `labs/`.
