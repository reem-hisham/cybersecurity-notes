# Lab — SQL Injection with Filter Bypass via XML Encoding

## Goal

Exploit a SQL injection vulnerability in the stock check feature to retrieve
the administrator's credentials and log in to the administrator account.

## What I Observed

The application:

* Used XML to send `productId` and `storeId`.
* Included the `storeId` value in a SQL query.
* Returned the query results in the application's response.
* Blocked obvious SQL injection payloads.

The vulnerable parameter was:

```text
storeId
```

## Identify the Vulnerability

The stock check request was sent to Burp Repeater.

I tested whether the `storeId` value was being evaluated by sending:

```xml
<storeId>1+1</storeId>
```

The application returned the stock for a different store, confirming that my
input was being evaluated.

I then tested a UNION attack:

```xml
<storeId>1 UNION SELECT NULL</storeId>
```

The request was blocked as a potential attack.

This indicated that a **WAF/filter** was blocking the SQL injection payload.

## WAF Bypass

Because the injection was being sent inside XML, I used **XML entity
encoding** to obfuscate the SQL injection payload.

I used the **Hackvertor** Burp extension to encode the payload using:

```text
dec_entities
hex_entities
```

After encoding the payload, the request was no longer blocked.

This confirmed that the filter could be bypassed using XML encoding.

## Determine the Number of Columns

I continued testing the UNION query and determined that the original query
returned **one column**.

When attempting to return more than one column, the application returned:

```text
0 units
```

indicating that the query was not valid.

## Data Extraction

Since only one column could be returned, I concatenated the username and
password into a single value:

```sql
1 UNION SELECT username || '~' || password FROM users
```

Encoded inside the XML:

```xml
<storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users</@hex_entities></storeId>
```

The application returned the usernames and passwords separated by `~`.

Example:

```text
username~password
```

I identified the administrator credentials and used them to log in.

## Result

Successfully retrieved the administrator credentials and logged in as the
administrator.

**Lab solved.**

## What I Learned

* XML can be an SQL injection attack surface.
* Burp Repeater is useful for manually testing XML parameters.
* A WAF can block obvious SQL injection payloads.
* XML entity encoding can be used to bypass some filters.
* UNION attacks require determining the number of returned columns.
* When only one column is available, multiple values can be concatenated.
* Hackvertor can automate encoding transformations during testing.

## Key Takeaway

The important lesson was that **a blocked payload does not necessarily mean
the underlying vulnerability is not exploitable**.

```text
SQL Injection
      ↓
Payload blocked by WAF
      ↓
XML entity encoding
      ↓
WAF bypass
      ↓
UNION SQL Injection
      ↓
Extract credentials
      ↓
Administrator login
```
