
# Cross-Site Scripting (XSS)

## Definition

**Cross-Site Scripting (XSS)** is a web vulnerability that occurs when
attacker-controlled input is processed by an application and reaches a
browser context where it can be interpreted as executable code.

The core idea is:

```text
Untrusted Input
      ↓
Web Application / JavaScript
      ↓
Browser
      ↓
Unexpected Code Execution
````

---

## Main Types of XSS

### 1. Reflected XSS

The malicious input is included in a request and immediately reflected in
the application's response.

```text
Request
   ↓
User Input
   ↓
Server
   ↓
Response containing input
   ↓
Browser
```

The vulnerability depends on where the reflected input is placed in the
response.

---

### 2. Stored XSS

The malicious input is stored by the application and later included in a
response to users.

```text
User Input
    ↓
Application
    ↓
Storage
    ↓
Later Response
    ↓
Browser
```

Because the input is stored, the vulnerability can affect users who later
view the affected content.

---

### 3. DOM-Based XSS

DOM-based XSS occurs when client-side JavaScript processes
attacker-controlled data in an unsafe way.

```text
User-Controlled Data
        ↓
JavaScript Source
        ↓
Data Processing
        ↓
Dangerous Sink
        ↓
Browser
```

The vulnerable behavior can occur entirely in the browser without the
server directly reflecting the payload.

---

## XSS Contexts

The same input can behave differently depending on where it is inserted.

Common contexts include:

* HTML content
* HTML attributes
* JavaScript
* CSS
* URLs
* DOM

Example:

```html
<p>USER_INPUT</p>
```

versus:

```html
<input value="USER_INPUT">
```

The **context** determines how the browser interprets the input.

---

## Sources and Sinks

This concept is especially important for DOM-based XSS.

### Source

A **source** is where attacker-controlled data enters client-side
JavaScript.

### Sink

A **sink** is a function or API that processes the data in a way that may
lead to unsafe interpretation.

Conceptually:

```text
Source
  ↓
Attacker-Controlled Data
  ↓
JavaScript
  ↓
Sink
  ↓
Browser
```

---

## Impact

Depending on the application's context and the victim's privileges, XSS
may allow an attacker to perform actions in a victim's browser context.

Potential impact can include:

* Performing actions as the victim.
* Accessing information available to the affected browser context.
* Modifying page content.
* Phishing or UI manipulation.
* Compromising application functionality available to the victim.

The actual impact depends on the application's architecture and security
controls.

---

## Encoding and Filtering

Applications may encode or filter user input before displaying or processing
it.

Important concepts include:

* HTML encoding
* JavaScript encoding
* URL encoding
* Input filtering
* Output encoding
* Context-specific escaping

A key principle is:

> **Encoding and escaping must be appropriate for the context in which the
> data is used.**

---

## Reflected vs Stored vs DOM

```text
Reflected XSS
Request → Server → Response → Browser

Stored XSS
Request → Server → Storage
                    ↓
                 Response
                    ↓
                 Browser

DOM XSS
Input → Client-side JavaScript → DOM / Browser
```

---

## Prevention

Important defensive measures include:

* Context-appropriate output encoding.
* Proper input handling.
* Avoiding unsafe DOM APIs when possible.
* Using safe APIs that treat data as data rather than executable content.
* Implementing an appropriate Content Security Policy (CSP).
* Using framework security features correctly.
* Validating and sanitizing input where appropriate.

Output encoding should be selected based on the **context**, rather than
using one generic encoding method everywhere.

---

## Key Takeaways

* XSS occurs when untrusted input reaches an executable browser context.
* The three main categories are **reflected, stored, and DOM-based XSS**.
* The context in which input appears is critical.
* DOM XSS requires understanding client-side data flow.
* Sources and sinks are important when analyzing JavaScript.
* Encoding and escaping must be context-specific.
* Understanding browser behavior is more valuable than memorizing payloads.

