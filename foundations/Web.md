# Web Architecture

## Core Architecture

```text
Browser
   ↓
HTTP
   ↓
Web Server
   ↓
Application / Backend
   ↓
Database
```

The response travels back through the same chain.

```text
Browser → Request → Server → Application → Database
Browser ← Response ← Server ← Application ← Database
```

## Frontend

Runs mainly on the client.

Examples:

```text
HTML
CSS
JavaScript
```

Responsible for:

* UI
* Client-side logic
* Sending requests
* Displaying responses

**Important:** Client-side validation is not a security boundary because requests can be modified or sent directly using tools such as Burp or curl.

## Backend / Application

Runs on the server and contains application logic.

Responsible for:

```text
Authentication
Authorization
Business Logic
Database interaction
File processing
API handling
```

## Web Server

Examples:

```text
Apache
Nginx
IIS
```

Common responsibilities:

* Accept HTTP connections
* Serve static files
* Handle TLS
* Route/forward requests
* Communicate with the application

**Web Server ≠ Web Application**

## API

An API provides an interface for clients to communicate with the application.

Example:

```http
GET /api/users/15
```

Possible response:

```json
{
  "id": 15,
  "username": "alice"
}
```

Clients can include:

```text
Browser
Mobile App
Another Server
```

## Database

Stores application data such as:

```text
Users
Passwords
Posts
Orders
Messages
Sessions
Products
```

Typical flow:

```text
HTTP Request
    ↓
Application
    ↓
Database Query
    ↓
Database
    ↓
Application
    ↓
HTTP Response
```

## Session

HTTP is stateless, so applications use sessions to maintain user state.

Typical flow:

```text
Login
 ↓
Authentication
 ↓
Session created
 ↓
Session ID sent to client
 ↓
Client sends session with future requests
```

Example:

```http
Cookie: session=abc123
```

## Authentication vs Authorization

### Authentication

```text
Who are you?
```

Examples:

```text
Username + Password
MFA
OAuth
API Key
```

### Authorization

```text
What are you allowed to do?
```

Example:

```text
Authenticated as Alice
        ↓
Can Alice access user #15?
Can Alice delete accounts?
Can Alice access /admin?
```

Authentication and authorization are different security controls.

## Pentester Mental Model

Don't think:

```text
"I am looking at a webpage."
```

Think:

```text
What does the client send?
        ↓
Where does the request go?
        ↓
What does the application do?
        ↓
What data does it access?
        ↓
What does it trust?
        ↓
What does it return?
```

## Key Idea

```text
Input
  ↓
Application
  ↓
Logic
  ↓
Database / Services
  ↓
Response
```

Security vulnerabilities often appear when the application:

* Trusts user-controlled input
* Fails to validate data
* Implements authentication incorrectly
* Implements authorization incorrectly
* Exposes sensitive data
* Handles files or commands unsafely
* Contains flawed business logic


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
