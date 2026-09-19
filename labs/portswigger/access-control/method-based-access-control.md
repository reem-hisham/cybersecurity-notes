# Method-Based Access Control Bypass

## Lab

**Method-based access control can be circumvented**

## Concept

Some applications enforce access control based on the **HTTP method** used to access an endpoint.

For example, a platform may restrict:

```http
POST /admin/deleteUser
```

while the application may still accept another method for the same action.

If the access-control layer blocks one method but the application accepts another, the restriction can potentially be bypassed.

---

## What to Look For

When I find a restricted endpoint, I should ask:

> **"Is the authorization decision tied to the HTTP method?"**

For example:

```http
POST /admin/action
```

I can investigate whether changing the method changes the application's behavior:

```http
GET /admin/action
```

The goal isn't to blindly try every HTTP method.

The goal is to identify a **mismatch between the access-control layer and the application**.

---

## Mental Model

```text
Request
   ↓
Access-control layer
   ↓
Checks URL + HTTP method
   ↓
Request passes
   ↓
Application handles request
```

A vulnerability can occur when these layers don't enforce the same rules.

```text
Access-control layer:
"GET is not restricted"

        ↓

Application:
"GET still performs the protected action"
```

---

## Pentester Mindset

When testing a sensitive endpoint:

```text
1. Identify the protected functionality.
2. Capture the request.
3. Identify the HTTP method.
4. Understand what the method is doing.
5. Test whether another method reaches the same functionality.
6. Compare the authorization behavior.
7. Confirm whether the protected action is actually performed.
```

### Key Takeaway

> **Don't assume that blocking one HTTP method protects the underlying functionality.**

The important vulnerability pattern is a **discrepancy between how access control handles the request and how the application processes it**.
