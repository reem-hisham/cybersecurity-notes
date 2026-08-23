# HTML Basics

## What Is HTML?

**HTML (HyperText Markup Language)** defines the structure of a web page.

Example:

```html
<h1>Welcome</h1>
<p>Hello world</p>
```

The browser parses the HTML and builds a document structure called the
**DOM (Document Object Model)**.

## Elements

HTML is built from elements.

```html
<p>Hello</p>
```

Here:

```text
<p> → opening tag
Hello → content
</p> → closing tag
```

## Attributes

Elements can contain attributes.

```html
<a href="/profile">Profile</a>
```

Here:

```text
href="/profile"
```

is an attribute.

Attributes are important when studying XSS because user-controlled input
may be inserted into an attribute context.

## HTML Contexts

User input can appear in different parts of HTML.

Example:

```html
<p>USER_INPUT</p>
```

or:

```html
<input value="USER_INPUT">
```

or:

```html
<script>
    // JavaScript context
</script>
```

The context in which input is placed affects how the browser interprets it.

## HTML Entities

HTML supports character entities.

For example:

```text
&amp;
&lt;
&gt;
&quot;
```

Entities allow special characters to be represented safely or differently
inside HTML.

## Why HTML Matters for XSS

XSS occurs when untrusted input reaches a browser context where it can be
interpreted as executable code.

Therefore, when testing XSS, an important question is:

> **Where exactly does my input appear in the HTML?**

## Key Takeaways

* HTML defines the structure of web pages.
* Elements contain content and can contain attributes.
* Browsers parse HTML into the DOM.
* User input can appear in different HTML contexts.
* The context determines how the browser interprets the input.
