# Lab: DOM XSS in jQuery Selector Sink Using a hashchange Event

## Goal

Exploit a DOM-based XSS vulnerability to execute the `print()` function in the victim's browser.

## What Made This Lab Different

There was no visible input field.

The attacker-controlled input came from the URL fragment:

```text
https://TARGET/#[input]
```

The vulnerable code used:

```text
location.hash
```

to read the value from the URL.

## Vulnerable Flow

```text
URL Fragment
      ↓
location.hash
      ↓
hashchange Event
      ↓
jQuery $() Selector Sink
      ↓
Injected HTML
      ↓
XSS Execution
```

## Important Discovery

The vulnerable JavaScript only processed the value when the `hashchange` event occurred.

Therefore, simply placing the payload in the URL was not enough.

The hash needed to be changed after the page loaded to trigger the vulnerable function.

## Exploit

```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
```

## How the Exploit Works

```text
1. iframe loads the vulnerable page with #

                ↓

2. iframe finishes loading

                ↓

3. onload event executes

                ↓

4. Malicious payload is appended to the URL hash

                ↓

5. URL hash changes

                ↓

6. hashchange event is triggered

                ↓

7. Vulnerable JavaScript reads location.hash

                ↓

8. Value reaches the jQuery $() sink

                ↓

9. Injected HTML is processed

                ↓

10. onerror executes print()
```

## Result

The `print()` function was successfully executed in the victim's browser.

**Lab solved.**

## What I Learned

* DOM XSS does not require a visible input field.
* Browser-controlled values such as `location.hash` can be an attacker-controlled source.
* Events can control when vulnerable JavaScript processes input.
* Understanding the execution flow is important when exploiting DOM XSS.
* `hashchange` is triggered when the URL fragment changes.
* An `iframe` and its `onload` event can be used to change the hash after the page loads.
* jQuery's `$()` function can act as a dangerous sink when attacker-controlled input reaches it.

## Key Takeaway

When testing for DOM XSS, I should not only look for input fields.

I should also look for:

```text
Attacker-controlled source
        ↓
JavaScript processing
        ↓
Event that triggers the code
        ↓
Dangerous sink
```

Understanding how the input flows through the JavaScript and what triggers the vulnerable code is essential for identifying and exploiting DOM-based XSS.
