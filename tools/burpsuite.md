# Burp Suite

## Purpose

**Burp Suite** is a web application security testing platform used to
intercept, inspect, modify, and replay HTTP/HTTPS traffic.

---

## Features I Used

### Proxy

Used to intercept requests between the browser and the target application.

```text
Browser
   ↓
Burp Proxy
   ↓
Web Application
```

Useful for:

* Inspecting requests and responses.
* Modifying parameters.
* Modifying cookies and headers.
* Testing user-controlled input.

---

### Repeater

Used to manually modify and resend HTTP requests.

Useful when:

* Testing payloads repeatedly.
* Comparing responses.
* Modifying parameters one at a time.
* Investigating application behavior.

Basic workflow:

```text
Intercept request
      ↓
Send to Repeater
      ↓
Modify request
      ↓
Send
      ↓
Analyze response
```

---

### Intruder

Used to automate sending multiple payloads or values.

I used it for:

* Testing SQLi conditions.
* Extracting values character by character.
* Automating repeated requests.

---

### Collaborator

Used to detect out-of-band interactions from a target.

```text
Target
   ↓
External interaction
   ↓
Burp Collaborator
   ↓
Interaction detected
```

I used it for OOB SQL injection labs to:

* Confirm that injected SQL executed.
* Detect DNS/HTTP interactions.
* Observe exfiltrated data.

---

## Common Workflow

```text
1. Intercept request
        ↓
2. Identify interesting input
        ↓
3. Send request to Repeater
        ↓
4. Modify/test input
        ↓
5. Analyze response
        ↓
6. Use Intruder when automation is useful
        ↓
7. Use Collaborator when testing OOB behavior
```

---

## Key Takeaways

* **Proxy** → Intercept and modify traffic.
* **Repeater** → Manually replay and modify requests.
* **Intruder** → Automate repeated requests.
* **Collaborator** → Detect out-of-band interactions.
