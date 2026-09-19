# URL-Matching Discrepancies

## Concept

Access-control bypasses can occur when different components of an application **interpret the same URL differently**.

For example:

```text
Access-control layer:
    /admin/deleteUser

Application:
    /admin/deleteUser/
```

If one component considers these different endpoints while another treats them as the same endpoint, the authorization check may be bypassed.

---

## Common Variations

### 1. Case Differences

```text
/admin/deleteUser
/ADMIN/DELETEUSER
```

The application may treat these paths as equivalent while the access-control mechanism does not.

---

### 2. Trailing Slash

```text
/admin/deleteUser
/admin/deleteUser/
```

Some systems treat both paths as the same endpoint, while another layer may distinguish them.

---

### 3. Path Suffix / Extension

Some frameworks may map:

```text
/admin/deleteUser.anything
```

to:

```text
/admin/deleteUser
```

The access-control layer may see a different path, while the application routes the request to the protected endpoint.

---

# The Real Vulnerability

The important part is **not** memorizing:

```text
/
uppercase
.anything
```

The underlying issue is:

> **Different components disagree about which endpoint the request targets.**

For example:

```text
Attacker
   ↓
/ADMIN/deleteUser
   ↓
Access-control layer
"Not the protected endpoint"
   ↓
Request allowed
   ↓
Application / Router
"This maps to /admin/deleteUser"
   ↓
Protected functionality executed
```

---

# What To Look For

When testing a restricted endpoint, consider whether its representation can change:

```text
/admin/deleteUser
/admin/deleteUser/
/ADMIN/deleteUser
/admin/deleteUser.anything
```

Then compare:

* HTTP response
* status code
* redirects
* application behavior
* whether the protected action is actually executed

---

# Pentester Mindset

When different layers are involved, ask:

> **"Do all components agree on what endpoint this request represents?"**

This is especially relevant when an application sits behind:

```text
Client
  ↓
Proxy / Web Server
  ↓
Access-control layer
  ↓
Framework / Router
  ↓
Application
```

A discrepancy between these layers can create an access-control bypass.

---

# Key Takeaway

> **URL normalization is part of the security boundary.**

If the access-control mechanism and the application normalize or match URLs differently, a seemingly protected endpoint may become reachable through an alternative representation.
