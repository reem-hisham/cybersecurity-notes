# DOM XSS in jQuery `href` Sink Using `location.search`

## Goal

Exploit DOM XSS to make the "back" link execute JavaScript and alert
`document.cookie`.

## What I Learned

The vulnerability used:

- **Source:** `location.search`
- **Sink:** jQuery `$()` modifying an `href` attribute

```text
location.search
      ↓
JavaScript
      ↓
href attribute
      ↓
JavaScript execution
````

### `javascript:` URL Scheme

I learned that JavaScript can be written in a URL using:

```text
javascript:YOUR_CODE
```

Example:

```text
javascript:alert(document.cookie)
```

If attacker-controlled input reaches an `href` in this form, clicking the
link can execute the JavaScript.

## Key Takeaway

DOM XSS can occur when attacker-controlled URL parameters reach a dangerous
HTML attribute such as `href`.

**Lab solved.**

