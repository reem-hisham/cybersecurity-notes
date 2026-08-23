# Cross-Site Scripting (XSS) Methodology

## 1. Identify User-Controlled Input

Look for input that is reflected, stored, or processed by client-side
JavaScript.

Common locations:

* URL parameters
* Query strings
* Form parameters
* HTTP headers
* Cookies
* JSON data
* Page fragments

---

## 2. Determine How the Input Is Handled

First determine whether the input is:

```text
Reflected
Stored
DOM-processed
```

The behavior determines which type of XSS may be possible.

---

## 3. Find the Context

The most important question is:

> **Where does my input appear?**

Possible contexts include:

```text
HTML content
HTML attribute
JavaScript
CSS
URL
DOM
```

Example:

```html
<p>USER_INPUT</p>
```

versus:

```html
<input value="USER_INPUT">
```

The same input can behave differently depending on its context.

---

## 4. Test Reflected XSS

For reflected XSS:

```text
Request
   ↓
User input
   ↓
Server processes input
   ↓
Response reflects input
   ↓
Browser
```

Determine whether the input is returned in a context where the browser may
interpret it as executable code.

---

## 5. Test Stored XSS

For stored XSS:

```text
User input
   ↓
Application
   ↓
Storage
   ↓
Later response
   ↓
Browser
```

Look for locations where user-controlled content is stored and later
displayed to users.

---

## 6. Test for DOM XSS

Inspect client-side JavaScript and look for data flowing from
user-controlled sources into sensitive sinks.

Conceptually:

```text
Source
  ↓
User-controlled data
  ↓
JavaScript processing
  ↓
Sink
```

The goal is to understand the **data flow**, rather than simply searching
for a particular payload.

---

## 7. Analyze Filtering and Encoding

If input is modified or blocked, determine what the application is doing
to it.

Check:

* HTML encoding
* JavaScript encoding
* URL encoding
* Input filtering
* Output transformations
* Context-specific escaping

Do not assume that a blocked test automatically means the input is safe.

---

## 8. Confirm the Vulnerability

A successful XSS test should demonstrate that attacker-controlled input can
reach an executable browser context.

During testing, focus on proving:

```text
User input
    ↓
Application / JavaScript
    ↓
Vulnerable context
    ↓
Browser interpretation
```

---

## 9. Document the Context

For each XSS vulnerability, record:

* Injection point
* XSS type
* Reflection/storage behavior
* HTML/JavaScript/DOM context
* Filtering or encoding
* Relevant source and sink
* What confirmed the vulnerability
* Security impact
* What I learned

Keep lab-specific payloads in the corresponding lab write-up.

---

## XSS Decision Flow

```text
Find user-controlled input
          ↓
Where does the input go?
          ↓
   ┌──────┼──────┐
   ↓      ↓      ↓
Reflected Stored  DOM
   ↓      ↓      ↓
Find the context / data flow
          ↓
Analyze encoding and filtering
          ↓
Confirm browser interpretation
          ↓
Document the vulnerability
```

## Key Principles

* **Context matters more than memorizing payloads.**
* Always determine where user input is inserted.
* Understand how the browser parses the resulting content.
* For DOM XSS, trace data from source to sink.
* Analyze encoding and filtering rather than guessing.
* Keep methodology separate from individual lab solutions.
