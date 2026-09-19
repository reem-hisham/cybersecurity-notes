# Referer-Based Access Control

## Lab

**Referer-based access control**

## Concept

The `Referer` HTTP header can indicate which page initiated a request.

A vulnerable application might use it as an authorization mechanism.

Example:

```http
GET /admin/deleteUser
Referer: https://example.com/admin
```

The application might effectively assume:

```text
Referer = /admin
      ↓
User came from admin page
      ↓
Allow request
```

The problem:

> **The client controls the request header.**

Therefore, the `Referer` header is not proof that the user is actually authorized to perform the action.

---

## Testing Mindset

When I see authorization logic involving a request header, ask:

> **"Can the client modify this value?"**

If yes, it should not be trusted as the actual authorization decision.

---

## Key Takeaway

```text
Client-controlled header
        ↓
Authorization decision
        ↓
Potential bypass
```

Authorization should be based on the user's actual permissions, not on a claim supplied by the client.

---

## General Lesson

This is another example of a broader access-control principle:

> **Never use client-controlled data as proof of privilege.**
