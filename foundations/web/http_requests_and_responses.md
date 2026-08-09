# HTTP Requests and Responses

## HTTP

**HTTP (Hypertext Transfer Protocol)** is used for communication between
clients and web servers.

Basic flow:

```text
Browser
   ↓
HTTP Request
   ↓
Web Server
   ↓
HTTP Response
   ↓
Browser
```

## HTTP Request

A request is sent by the client to the server.

Example:

```http
GET /products?category=Gifts HTTP/1.1
Host: example.com
Cookie: TrackingId=abc123
```

Important parts include:

* HTTP method
* URL/path
* Parameters
* Headers
* Cookies
* Request body

## HTTP Response

The server responds with:

* Status code
* Headers
* Response body

Example:

```http
HTTP/1.1 200 OK
Content-Type: text/html
```

## HTTP Methods

Common methods:

```text
GET
POST
PUT
PATCH
DELETE
```

`GET` is commonly used to retrieve resources.

`POST` is commonly used to submit data to the server.

## Parameters

Parameters allow data to be sent to the server.

Example:

```text
/products?category=Gifts
```

Here:

```text
category=Gifts
```

is a parameter.

Parameters can also appear in request bodies, headers, and cookies.

## Why This Matters for Web Security

Security testing often involves identifying **user-controlled input** inside
HTTP requests.

Examples:

```text
URL parameter
POST parameter
Cookie
Header
JSON field
```

That input may then be processed by the application.

## Key Takeaways

* HTTP requests carry data from the client to the server.
* HTTP responses carry data back to the client.
* Parameters, headers, cookies, and request bodies can contain user input.
* Burp Suite can be used to inspect and modify HTTP traffic.
