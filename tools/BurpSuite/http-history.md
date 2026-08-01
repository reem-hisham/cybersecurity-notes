# Burp Suite — HTTP History

## What Is It?

HTTP History records the HTTP requests and responses that pass through
Burp Proxy.

## What I Use It For

- Reviewing requests after they have been sent
- Finding interesting endpoints
- Inspecting parameters
- Reviewing cookies and headers
- Finding requests to send to Repeater or Intruder

## What I Look At

For each request, I can inspect:

- HTTP method
- URL
- Parameters
- Headers
- Cookies
- Request body
- Response
- Status code

## What I Learned

HTTP History is useful when I want to go back and examine requests that I
already made instead of intercepting the same request again.

It also helps me identify which requests and parameters are worth testing.