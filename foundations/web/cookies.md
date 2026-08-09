# HTTP Cookies

## What Is a Cookie?

A **cookie** is data stored by the browser and sent to the server with
subsequent requests.

Example:

```http
Cookie: TrackingId=abc123
```

The server can use cookies to store or identify information related to a
client.

## Common Uses

Cookies can be used for:

* Session identification
* Authentication
* Tracking
* Preferences
* Analytics

## Cookie Flow

```text
Server
   ↓
Set-Cookie
   ↓
Browser stores cookie
   ↓
Browser sends cookie with requests
   ↓
Server processes cookie
```

## Security Importance

Cookies are part of the HTTP request and can therefore become an input
point for vulnerabilities.

For example:

```http
Cookie: TrackingId=abc123
```

If an application directly uses the cookie value in a database query, the
cookie may become an SQL Injection attack surface.

This is what happened in the PortSwigger SQL Injection labs.

## Key Takeaways

* Cookies are sent with HTTP requests.
* Cookies can contain application or tracking data.
* Cookies can be user-controlled input.
* Security testing should include cookies when looking for injection
  points.
