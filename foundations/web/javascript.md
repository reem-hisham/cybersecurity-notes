# JavaScript Basics

## What Is JavaScript?

**JavaScript** is a programming language commonly used to add behavior and
interactivity to web pages.

Example:

```javascript
const message = "Hello";
console.log(message);
```

JavaScript can interact with the HTML document through the DOM.

## Variables

Variables store values.

```javascript
const username = "admin";
```

## Functions

Functions contain reusable logic.

```javascript
function greet(name) {
    return "Hello " + name;
}
```

## The Browser and JavaScript

A simplified flow is:

```text
HTML
  ↓
Browser parses page
  ↓
DOM
  ↓
JavaScript interacts with DOM
  ↓
Page changes / behavior occurs
```

## Sources and Sinks

This concept is particularly important for DOM-based XSS.

A **source** is a location from which JavaScript can obtain
attacker-controlled data.

A **sink** is a function or API that uses that data in a potentially
dangerous way.

Conceptually:

```text
User-controlled data
        ↓
      Source
        ↓
   JavaScript
        ↓
       Sink
```

## Why JavaScript Matters for XSS

XSS is ultimately about getting untrusted data interpreted as code in a
browser context.

Understanding JavaScript helps explain:

* How client-side code processes input.
* How data moves through a page.
* How DOM-based vulnerabilities occur.
* Why different JavaScript contexts behave differently.

## Key Takeaways

* JavaScript adds behavior to web pages.
* JavaScript can interact with the DOM.
* Client-side code can process user-controlled data.
* Sources and sinks are important concepts for DOM-based XSS.
