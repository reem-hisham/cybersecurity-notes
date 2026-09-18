# JavaScript for Web Security

I don't need JavaScript developer-level knowledge.

My goal is to read JavaScript from a security perspective and trace
user-controlled data through the application.

## Core Mental Model

```text
Source
  ↓
Processing
  ↓
Data
  ↓
Sink
````

## Important Sources

```javascript
location
location.search
location.hash
URLSearchParams
document.cookie
localStorage
sessionStorage
```

## Important HTTP APIs

```javascript
fetch()
XMLHttpRequest
```

These can reveal how the frontend communicates with backend APIs.

## Important DOM APIs / Sinks

```javascript
innerHTML
outerHTML
document.write()
eval()
textContent
```

Not every source → sink flow is vulnerable.

I need to understand what the sink does with attacker-controlled data.

## URLSearchParams

```javascript
const params = new URLSearchParams(location.search);
const q = params.get("q");
```

This extracts a parameter from the URL.

Example:

```text
/search?q=laptop
```

```javascript
params.get("q")
```

returns:

```text
laptop
```

## Data Flow Example

```javascript
const q = new URLSearchParams(location.search).get("q");

document.getElementById("result").innerHTML = q;
```

Data flow:

```text
location.search
      ↓
URLSearchParams
      ↓
q
      ↓
innerHTML
```

## fetch()

```javascript
fetch("/api/user")
```

JavaScript is making an HTTP request to an API endpoint.

When I see `fetch()` I should investigate:

* endpoint
* HTTP method
* parameters
* request body
* authentication
* response
* how the response is used

## Cookies

```javascript
document.cookie
```

JavaScript can access cookies that are not protected by `HttpOnly`.

Important:

```text
HttpOnly cookie
    ↓
JavaScript cannot normally read it with document.cookie
```

## Browser Storage

```javascript
localStorage.getItem("key")
sessionStorage.getItem("key")
```

I should check what the application stores there.

It may contain harmless preferences or security-sensitive data such as tokens.

## Security Mindset

When reading JavaScript:

1. Find user-controlled input.
2. Identify the source.
3. Follow the data.
4. Understand transformations.
5. Identify the sink.
6. Ask what the sink does.
7. Determine whether attacker-controlled data can change application behavior.

The goal is not to understand every line of JavaScript.

The goal is to understand the application's data flow.