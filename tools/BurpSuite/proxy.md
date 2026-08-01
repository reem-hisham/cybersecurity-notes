# Burp Suite — Proxy

## What Is It?

Proxy allows me to intercept HTTP/HTTPS traffic between my browser and
the target application.

## What I Use It For

- Intercepting requests
- Inspecting requests and responses
- Modifying parameters before sending requests
- Forwarding or dropping requests
- Sending requests to Repeater or Intruder

## Basic Workflow

```text
Browser
   ↓
Burp Proxy
   ↓
Target Application
```

When interception is enabled, Burp pauses the request so I can inspect or
modify it before forwarding it.

## What I Can Inspect

A request can contain:

 * HTTP method
 * URL/path
 * Query parameters
 * Headers
 * Cookies
 * Request body

## What I Learned
I can intercept requests instead of only interacting with the application
through the browser.
Parameters can be modified before the request reaches the server.
Cookies and headers can also be important when testing web applications.
Interesting requests can be sent directly to Repeater or Intruder.