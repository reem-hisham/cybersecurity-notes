# Basic Access Control Labs

## 1. Unprotected Admin Functionality

### Objective

Access the admin functionality as a normal user.

### Approach

The application did not expose the admin functionality through the normal interface, so I looked for the admin endpoint directly.

### Result

The admin functionality was accessible without proper authorization.

### Key Takeaway

Hiding an admin endpoint or simply not linking to it is **not access control**.

> If the server does not verify the user's privileges, knowing the endpoint may be enough to access it.

---

## 2. Unprotected Admin Functionality with Unpredictable URL

### Objective

Access an admin function that uses an unpredictable URL.

### Approach

The admin endpoint was not obvious, so I inspected the application and looked for information that could reveal the endpoint.

### Result

The admin functionality could still be accessed despite the URL being difficult to guess.

### Key Takeaway

An unpredictable URL is **not a security boundary**.

This is another example of **security through obscurity**.

> The important question is not whether the endpoint is easy to guess.
> The important question is whether the server checks authorization.

---

## 3. User Role Controlled by Request Parameter

### Objective

Access admin functionality as a normal user.

### Observation

The application used a client-controlled value to determine the user's role.

For example:

```http
GET /admin?role=user
```

### Approach

I inspected the request and identified the parameter responsible for the user's role.

I then tested whether modifying that value affected the authorization decision.

### Result

Changing the client-controlled role value allowed access to functionality intended for a higher-privileged user.

### Key Takeaway

> Never trust client-controlled values for authorization decisions.

The server should determine the user's privileges from trusted server-side data.

---

## 4. User Role Can Be Modified in User Profile

### Objective

Escalate from a normal user to an administrative role.

### Observation

The user's profile contained information related to their role.

### Approach

I inspected the profile update request and checked whether the role value was sent by the client.

I tested whether modifying that value changed the account's privileges.

### Result

The role could be modified through the profile functionality, resulting in elevated privileges.

### Key Takeaway

Whenever a request updates account information, ask:

> **"Can I modify something that should only be controlled by the server?"**

This is especially interesting when the request contains:

* role
* privilege
* isAdmin
* user type
* account status

---

# General Pattern

These labs demonstrate different forms of the same underlying problem:

```text
User-controlled input
        ↓
Server trusts it
        ↓
Authorization decision
        ↓
Unauthorized functionality
```

The important mindset is:

> **What does the application trust when deciding what I am allowed to do?**

---

# What I Learned

* Admin functionality must be protected server-side.
* Hiding an endpoint does not provide authorization.
* Unpredictable URLs are not a security boundary.
* Client-controlled role/privilege values are dangerous.
* Authorization decisions should rely on trusted server-side information.
* When testing access control, inspect requests rather than only looking at the UI.
