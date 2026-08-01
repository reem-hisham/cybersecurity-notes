# Burp Suite — Repeater

## What Is It?

Repeater allows me to manually modify and resend an HTTP request multiple
times.

## What I Use It For

Repeater is useful when I want to:

- Test different payloads
- Modify one parameter at a time
- Compare responses
- Understand how the application reacts to different inputs
- Manually test vulnerabilities

## Basic Workflow

1. Intercept or find an interesting request.
2. Send it to Repeater.
3. Modify the desired parameter.
4. Send the request.
5. Inspect the response.
6. Change the input and test again.

## What I Learned

Repeater is useful when I want precise control over a request and want to
test different inputs manually without repeatedly interacting with the
browser. 
It is especially useful for testing parameters involved in vulnerabilities
such as SQL injection.