# DNS

## What Is DNS?

**DNS (Domain Name System)** translates domain names into IP addresses.

For example:

```text
example.com
     ↓
DNS lookup
     ↓
IP address
```

This allows applications to use domain names instead of directly using
IP addresses.

## Basic DNS Flow

```text
Client
   ↓
DNS Query
   ↓
DNS Server
   ↓
IP Address
```

## DNS in OOB SQL Injection

During the OOB SQL Injection labs, the database was induced to interact
with an external domain.

Conceptually:

```text
SQL Injection
      ↓
Database
      ↓
External hostname
      ↓
DNS lookup
      ↓
Burp Collaborator
```

The DNS interaction became an **observable signal** that could be detected
outside the vulnerable application.

## Important Distinction

DNS is not itself SQL Injection.

It was used as the **external communication channel** in the OOB technique
used in the lab.

The exact mechanism for triggering external interactions depends on the
database system and its available functionality.

## Key Takeaways

* DNS maps domain names to IP addresses.
* A hostname may need to be resolved before connecting to a service.
* DNS requests can be observed by infrastructure such as Burp
  Collaborator.
* DNS can therefore act as an out-of-band communication channel in some
  security testing scenarios.
