# DOM XSS in `document.write` Sink Using `location.search`

## What I Learned

The vulnerability used:

- **Source:** `location.search`
- **Sink:** `document.write`
- The input was inserted inside a `<select>` element.

```text
location.search
      ↓
document.write()
      ↓
<select>
      ↓
XSS
````

### Multiple Query Parameters

I learned that multiple query parameters in a URL are separated using `&`.

Example:

```text
?productId=1&storeId=2
```

Each parameter can then be accessed separately through `location.search`.

### Breaking Out of an HTML Context

Because my input was placed inside a `<select>` element, I needed to first break out of the existing HTML context before injecting another HTML element.

Conceptually:

```text
Input inside <select>
        ↓
Break out of </select>
        ↓
Inject HTML
        ↓
JavaScript execution
```

## Key Takeaways

* `location.search` can be a DOM XSS source.
* `document.write()` can become a dangerous sink.
* The HTML context determines how the input must be handled.
* Multiple URL query parameters are separated with `&`.
* DOM XSS can require breaking out of an existing HTML element.

**Lab solved**
