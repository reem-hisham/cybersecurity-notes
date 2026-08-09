# OOB SQL Injection — Understanding the Mechanism

## What Is OOB SQL Injection?

**Out-of-Band (OOB) SQL Injection** is a technique where the attacker uses the database to make an **external network interaction** that can be observed.

Instead of relying on the vulnerable application's HTTP response, SQL error, or response timing, the **external interaction becomes the information channel**.

```text
Attacker
   ↓
Web Application
   ↓
Database
   ↓
External Server
   ↓
Attacker observes the interaction
```

---

## Why Use OOB SQLi?

OOB SQLi is useful when the normal SQLi information channels are not useful.

For example:

```text
Can I observe the SQL result?
        ↓
       No
        ↓
Can I observe an error?
        ↓
       No
        ↓
Can I use response timing?
        ↓
       No
        ↓
Consider OOB
        ↓
External interaction
        ↓
Burp Collaborator
```

The main idea is:

> Instead of waiting for the vulnerable application to reveal the result, make the database produce an external interaction that can be observed.

---

# Burp Collaborator

**Burp Collaborator** acts as an external endpoint that can detect interactions from the target.

In the PortSwigger lab:

```text
Database
    ↓
External interaction
    ↓
Burp Collaborator
```

If Collaborator receives an interaction, it provides evidence that the injected SQL caused the database to perform the intended external action.

---

# The Oracle/XML OOB Technique

The lab used an **Oracle/XML-based technique**.

The overall chain is:

```text
SQL Injection
      ↓
Oracle executes injected SQL
      ↓
XMLTYPE()
      ↓
Oracle processes attacker-controlled XML
      ↓
DOCTYPE / external entity
      ↓
External URL
      ↓
External network interaction
      ↓
Burp Collaborator
```

The important point is that the SQL injection itself does **not magically create a DNS request**.

Instead, the SQL injection gives us the ability to make Oracle execute functionality that can process XML and potentially cause an external interaction.

---

# 1. SQL Injection

The payload first uses SQL injection to execute our own SQL.

Conceptually:

```sql
original query
+
UNION SELECT ...
```

Because the database in the lab is Oracle, the payload uses:

```sql
FROM dual
```

The SQL injection is simply the entry point that allows us to reach the Oracle functionality.

---

# 2. `XMLTYPE()`

`XMLTYPE()` is an Oracle function used to treat a string as XML.

Example:

```sql
XMLTYPE('<root>Hello</root>')
```

Conceptually:

```text
String containing XML
        ↓
    XMLTYPE()
        ↓
Oracle processes it as XML
```

It is better to think of `XMLTYPE()` as:

> **Taking a string containing XML and making Oracle interpret/process it as XML.**

It is **not** simply "executing XML code."

---

# 3. The XML Document

The injected string contains an XML document.

Conceptually, it contains something like:

```xml
<?xml version="1.0"?>
<!DOCTYPE root [
    <!ENTITY remote SYSTEM "http://external-domain/">
]>
```

The important part is the external entity:

```xml
<!ENTITY remote SYSTEM "http://external-domain/">
```

This associates the entity `remote` with an external resource.

Conceptually:

```text
remote
   ↓
external URL
```

---

# 4. External Entity

The important concept is **external entity resolution**.

If the XML processor resolves the external entity, it may need to access the external resource specified by the URL.

Conceptually:

```text
External entity
      ↓
Contains external URL
      ↓
XML processor resolves it
      ↓
External resource is accessed
      ↓
Network interaction
```

This is the bridge between the Oracle XML functionality and the OOB interaction.

---

# 5. Why Does DNS Happen?

Suppose the external resource is:

```text
http://BURP-COLLABORATOR-SUBDOMAIN/
```

Before Oracle can communicate with that hostname, the hostname may need to be resolved to an IP address.

Conceptually:

```text
Oracle
  ↓
Resolve hostname
  ↓
DNS request
  ↓
Collaborator infrastructure
```

Depending on the mechanism and environment, an HTTP URL can also result in further network communication after DNS resolution:

```text
DNS lookup
    ↓
TCP connection
    ↓
HTTP request
```

Therefore, don't memorize:

> "This payload = DNS."

Instead understand:

> **The payload causes an external interaction. DNS may be one observable part of that interaction.**

---

# 6. What Is `EXTRACTVALUE()` Doing?

The payload also uses:

```sql
EXTRACTVALUE(...)
```

This is another Oracle XML functionality used as part of the XML-processing chain.

A useful mental model is:

```text
XMLTYPE()
    ↓
Treat the string as XML
    ↓
EXTRACTVALUE()
    ↓
Process/query the XML
    ↓
External entity can be resolved
    ↓
External interaction
```

`EXTRACTVALUE()` itself should **not** be thought of as "the DNS function."

It is part of the Oracle/XML processing mechanism used by this particular technique.

---

# 7. What Does `%remote` Mean?

The payload defines an entity called `remote`.

Conceptually:

```text
remote
   ↓
external resource
```

The payload then references that entity.

The important idea is:

> **The XML processor is instructed to resolve an entity whose definition points to an external resource.**

The exact `%remote` syntax belongs to the XML/DTD mechanism and is something that should be studied separately rather than memorized as part of the SQLi payload.

---

# Complete Mental Model

The entire technique can now be understood as:

```text
TrackingId
    ↓
SQL Injection
    ↓
UNION SELECT
    ↓
Oracle
    ↓
XMLTYPE()
    ↓
Attacker-controlled XML
    ↓
DOCTYPE
    ↓
External Entity
    ↓
External URL
    ↓
XML processor resolves external resource
    ↓
External network interaction
    ↓
Burp Collaborator
```

---

# OOB vs Other Blind SQLi Techniques

The main difference is the **information channel**.

### Conditional Response

```text
TRUE  → "Welcome back"
FALSE → Normal response
```

### Conditional Error

```text
TRUE  → SQL error
FALSE → Normal response
```

### Time-Based

```text
TRUE  → Delayed response
FALSE → Normal response
```

### OOB

```text
SQL condition / execution
        ↓
External interaction
        ↓
Collaborator detects it
```

The common idea is:

> **Find an observable difference or external signal that allows you to determine what happened inside the database.**

---

# Important: Don't Memorize the Payload

The Oracle/XML payload is complicated because several technologies are combined:

```text
SQL
 ↓
Oracle
 ↓
XMLTYPE()
 ↓
XML
 ↓
DOCTYPE
 ↓
DTD
 ↓
External Entity
 ↓
URL
 ↓
Network interaction
```

The goal is to understand **why each layer exists**, not memorize the complete payload.

---

# What I Understand vs What I Still Need to Learn

## I Understand ✅

* What OOB SQL injection means
* Why OOB can be useful for blind SQLi
* The role of Burp Collaborator
* The external interaction as the information channel
* The role of the external URL
* The basic Oracle → XML → external interaction chain
* Why DNS can appear during the process
* The high-level purpose of `XMLTYPE()`
* The purpose of an external entity

## Still Need to Learn

* `EXTRACTVALUE()` in more depth
* XML `DOCTYPE`
* DTDs
* XML external entities
* `%remote` syntax
* The exact Oracle/XML payload construction
* Other Oracle mechanisms that can produce external interactions
* How OOB techniques differ between DBMSs

---

# Core Rule

Don't think:

```text
"This payload performs DNS."
```

Think:

```text
"OOB SQLi uses a database capability to cause
an external interaction that I can observe."
```

The exact mechanism depends on the **database system and available functionality**.

The Oracle/XML payload from this lab is **one implementation of the OOB concept**, not a universal OOB SQLi payload.
