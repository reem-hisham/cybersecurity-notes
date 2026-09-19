# Access Control

Access control answers one main question:

> **Is this user actually allowed to perform this action on this resource?**

A vulnerability happens when the application **fails to enforce that restriction on the server side**.

---

## 1. Vertical Privilege Escalation

**Vertical = different privilege levels**

Example:

```text
Normal User
    ↓
tries to access
    ↓
Admin functionality
```

If a normal user can perform an action that should only be available to an admin, this is **vertical privilege escalation**.

### Common things to look for

* Admin panels or admin-only endpoints
* Sensitive functionality hidden from normal users
* Role/privilege values controlled by the client
* Authorization checks based only on URL or request parameters

### Example

```http
GET /admin/deleteUser?id=123
```

If the server doesn't actually verify that the current user is an admin, hiding the link isn't enough.

### Key mindset

> **"This functionality should be restricted to a higher-privileged user. Does the server actually enforce that?"**

---

# 2. Method-Based Access Control

Sometimes access control is enforced based on both:

```text
URL + HTTP Method
```

For example:

```http
POST /admin/deleteUser
```

The application may expect:

```text
POST → allowed only for admins
GET  → not allowed
```

But the application itself might accept both methods:

```http
GET /admin/deleteUser?id=123
```

If the front-end/platform blocks the `GET` request but the application still performs the action when using `GET`, the restriction can potentially be bypassed.

### What to look for

When you find a restricted endpoint:

```text
POST /admin/action
```

ask:

> **"Does this action behave differently if I change the HTTP method?"**

Potential variations:

```http
POST → GET
POST → PUT
POST → DELETE
```

The important point isn't memorizing these combinations.

It's recognizing the **mismatch between the access-control layer and the application**.

### Mental model

```text
Access Control:
POST /admin/action → BLOCKED

Application:
GET /admin/action → still performs the action

             ↓

      Access Control Bypass
```

---

# 3. URL-Matching Discrepancies

Another class of access-control bypass happens when **different components interpret the URL differently**.

For example:

```text
Access-control layer:
    /admin/deleteUser
```

But the application/router may also accept:

```text
/admin/deleteUser/
/ADMIN/DELETEUSER
/admin/deleteUser.anything
```

If the security layer and the application don't agree that these URLs refer to the same endpoint, the restriction can be bypassed.

---

## Common variations

### 1. Case differences

```text
/admin/deleteUser
/ADMIN/DELETEUSER
```

If the application treats them as equivalent but the access-control mechanism doesn't, you can get a mismatch.

---

### 2. Trailing slash

```text
/admin/deleteUser
/admin/deleteUser/
```

Some frameworks treat these as the same endpoint, while another layer may treat them as different.

---

### 3. File extensions / suffixes

Some frameworks can map:

```text
/admin/deleteUser.anything
```

to:

```text
/admin/deleteUser
```

So the security layer might see a different path while the application still reaches the protected functionality.

---

# The Important Idea

Don't memorize:

```text
uppercase
trailing slash
.anything
```

as random bypass tricks.

Understand the underlying problem:

> **Two components disagree about what endpoint a request is targeting.**

For example:

```text
Attacker Request
       ↓
┌─────────────────────┐
│ Access Control Layer│
│ "This isn't /admin" │
└──────────┬──────────┘
           ↓
      Request passes
           ↓
┌─────────────────────┐
│ Application / Router│
│ "This IS /admin"    │
└──────────┬──────────┘
           ↓
   Protected action
```

That's the vulnerability pattern.

---

# Pentester Mindset

When testing access control, don't only ask:

> "Can I access `/admin`?"

Also ask:

### About the privilege

> Who should be allowed to do this?

### About the request

> Which part of the request determines the action?

### About the method

> Does changing `POST` to `GET` change the authorization behavior?

### About the URL

> Does the application interpret slightly different paths as the same endpoint?

### About the architecture

> Does the proxy, WAF, web server, framework, and application all interpret this request the same way?

---

# Quick Recognition Checklist

When you discover a sensitive endpoint:

```text
[ ] Who should have access?
[ ] Am I testing as a lower-privileged user?
[ ] Is authorization enforced server-side?
[ ] Does changing the HTTP method affect access?
[ ] Does changing the URL representation affect access?
[ ] Is there a trailing-slash difference?
[ ] Is case handled consistently?
[ ] Does adding a suffix/extension change the behavior?
[ ] Are different layers interpreting the request differently?
```

---

# One-Line Memory

**Vertical:**

> Can a lower-privileged user perform a higher-privileged action?

**Method-based bypass:**

> Does another HTTP method reach the same protected action?

**URL-matching discrepancy:**

> Do different components disagree about which endpoint the request targets?

---

## What you've covered today

```text
Access Control
│
├── Vertical Privilege Escalation       ✓
│   ├── Unprotected functionality
│   ├── Unpredictable URL
│   ├── Parameter-based role control
│   ├── User-controlled role
│   └── URL-based restriction bypass
│
├── Method-Based Access Control         ✓
│
└── URL-Matching Discrepancies          ✓
    ├── Case differences
    ├── Trailing slash
    └── Path suffix / extension
```

### The bigger pattern

```text
Client-controlled request
        ↓
What does the server trust?
        ↓
Does the authorization check
match what the application actually executes?
        ↓
If NOT → possible access-control bypass
```
