# Burp Suite

## What Is Burp Suite?

Burp Suite is a web application security testing tool used to intercept,
inspect, modify, and replay HTTP/HTTPS requests.

It allows me to see how a web application communicates with the server and
modify requests before they reach the application.

## Main Workflow

A basic Burp Suite workflow is:

1. Configure the browser to use Burp as a proxy.
2. Intercept a request with Proxy.
3. Inspect the request and response.
4. Send interesting requests to Repeater or Intruder.
5. Modify parameters and test the application's behavior.
6. Analyze the responses.

## Main Tools I Use

- Proxy — intercept and modify requests
- HTTP History — view requests and responses
- Repeater — manually modify and resend requests
- Intruder — automate repeated requests with different inputs

## What I Learned

- Burp can act as a proxy between my browser and the target application.
- I can inspect HTTP requests before they reach the server.
- I can modify parameters such as cookies, headers, and form values.
- I can send interesting requests to other Burp tools for further testing.