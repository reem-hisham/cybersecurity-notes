# Multi-Step Process Access Control Bypass

## Lab

**Multi-step process with no access control on one step**

## Concept

Some sensitive functionality is implemented through multiple requests.

For example:

```text
Step 1 → Load form
Step 2 → Submit changes
Step 3 → Confirm action
```

A vulnerable application might enforce authorization on Steps 1 and 2 but forget to enforce it on Step 3.

```text
Step 1 → Authorization ✓
Step 2 → Authorization ✓
Step 3 → Authorization ✗
```

If Step 3 performs the actual sensitive action, an attacker may be able to access it directly.

---

## Testing Approach

When I encounter a multi-step process:

1. Capture every request.
2. Identify what each step actually does.
3. Determine which request performs the sensitive action.
4. Test whether each step independently checks authorization.
5. Try accessing later steps directly when appropriate.

### Mental Model

> **Never assume that reaching Step 3 through Step 1 automatically proves authorization for Step 3.**

Each sensitive endpoint should enforce authorization independently.

---

## Key Takeaway

A multi-step workflow is not a single authorization check.

```text
Workflow
   ↓
Request 1
Request 2
Request 3 ← sensitive action
```

Every security-sensitive request must enforce its own authorization.
