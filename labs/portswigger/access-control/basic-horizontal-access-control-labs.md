# PortSwigger — Horizontal Access Control Labs

## 1. Insecure Direct Object References

### Objective

Access another user's resource by manipulating an object reference.

### Approach

I identified a request containing a user-controlled object identifier and tested whether changing the identifier changed the accessed resource.

Example:

```http
GET /myaccount?id=123
```

I tested another identifier:

```http
GET /myaccount?id=456
```

### Result

The application returned another user's resource without properly verifying ownership.

### Key Takeaway

> Never assume that because a user can control an object ID, they are authorized to access that object.

---

## General Pattern

```text
Object ID controlled by client
            ↓
       Change ID
            ↓
     Different object
            ↓
Server checks ownership?
       ↓           ↓
      Yes          No
       ↓            ↓
   Access denied   IDOR
```

---

## 2. User ID Controlled by Request Parameter with Password Disclosure

### Objective

Access another user's account information by manipulating a user identifier.

### Approach

I modified the user ID parameter to reference another account.

The important part was not simply accessing another user's page, but observing what **sensitive information** that page exposed.

### Result

The manipulated request exposed sensitive account information, including information that could be used to compromise the targeted account.

### Key Takeaway

A horizontal access-control flaw can become much more serious when the accessed resource belongs to a **privileged user**.

```text
Horizontal access
      ↓
Admin's account
      ↓
Sensitive information
      ↓
Potential account compromise
      ↓
Vertical privilege escalation
```
