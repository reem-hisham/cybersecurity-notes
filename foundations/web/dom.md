# DOM Basics

## What Is the DOM?

The **DOM (Document Object Model)** is the browser's representation of an
HTML document.

Conceptually:

```text
HTML
  ↓
Browser parses HTML
  ↓
DOM Tree
  ↓
JavaScript interacts with the DOM
```

Example HTML:

```html
<p>Hello</p>
```

The browser represents this as an element in the DOM.

## JavaScript and the DOM

JavaScript can read and modify elements in the DOM.

For example:

```javascript
document.getElementById("message")
```

can be used to access an element with the corresponding ID.

## DOM-Based XSS

DOM-based XSS occurs when client-side JavaScript takes
attacker-controlled data and processes it in an unsafe way.

Conceptually:

```text
User-controlled input
        ↓
JavaScript source
        ↓
DOM processing
        ↓
Dangerous sink
        ↓
Browser interprets result
```

Unlike reflected XSS, the vulnerable behavior can occur entirely on the
client side.

## Sources and Sinks

Common source/sink concepts are useful when analyzing client-side
JavaScript.

```text
Source
  ↓
User-controlled data
  ↓
JavaScript processing
  ↓
Sink
```

The important question is:

> **Can attacker-controlled data flow from a source to a dangerous sink?**

## Why the DOM Matters for XSS

Understanding the DOM helps explain the difference between:

```text
Reflected XSS
Server reflects input → Browser

Stored XSS
Server stores input → Browser

DOM XSS
JavaScript processes input → Browser
```

## Key Takeaways

* The DOM represents the web page inside the browser.
* JavaScript can read and modify the DOM.
* DOM-based XSS involves unsafe client-side processing of
  attacker-controlled data.
* Understanding sources, data flow, and sinks is important for DOM XSS.
