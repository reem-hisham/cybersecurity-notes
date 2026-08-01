# Burp Suite — Intruder

## What Is It?

Intruder is used to automate sending many requests with different input
values.

## What I Use It For

- Testing many payloads
- Brute-force-style testing in authorized labs
- Testing different characters or values
- Finding valid input among many possibilities
- Automating repetitive requests

## Basic Workflow

1. Send a request to Intruder.
2. Select the parameter or position to test.
3. Mark the position as a payload location.
4. Choose or provide a payload list.
5. Start the attack.
6. Compare the responses to identify interesting results.

## Example

During a PortSwigger blind SQL injection lab, I used Intruder to test:

- Different password lengths
- Different character positions
- Different possible characters

Instead of sending every request manually, Intruder automated the process.

## What I Learned

Intruder is useful when the same request needs to be sent many times with
different values.

The response differences can help identify which payload produced the
desired result.