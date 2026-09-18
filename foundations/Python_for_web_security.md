# Python for Security

I don't need to learn all of Python.

I need enough Python to understand, automate, and modify security-related tasks.

## Core Goal

Use Python as a security tool language.

The goal is not to build tools better than Nmap, Burp, ffuf, etc.

The goal is to understand what these tools are doing.

## Core Python Concepts

```text
variables
conditions
loops
functions
lists
dictionaries
strings
files
requests
subprocess
regex
````

## Security Mental Model

```text
Input
  ↓
Processing
  ↓
Action
  ↓
Response
  ↓
Analysis
```

## HTTP with Python

```python
import requests

response = requests.get(
    "https://example.com",
    timeout=5
)

print(response.status_code)
print(response.headers)
print(response.text)
```

Important response properties:

```python
response.status_code
response.headers
response.text
```

## GET Parameters

```python
params = {
    "q": "laptop"
}

response = requests.get(
    "https://example.com/search",
    params=params
)
```

## POST Data

```python
data = {
    "username": "test",
    "password": "password"
}

response = requests.post(
    "https://example.com/login",
    data=data
)
```

## Headers

```python
headers = {
    "User-Agent": "Mozilla/5.0"
}

response = requests.get(
    "https://example.com",
    headers=headers
)
```

## Cookies

```python
cookies = {
    "session": "test-session"
}

response = requests.get(
    "https://example.com/account",
    cookies=cookies
)
```

## Files

```python
with open("paths.txt", "r") as file:
    paths = file.read().splitlines()
```

Useful for wordlists and input sets.

## Loops

```python
for path in paths:
    print(path)
```

Useful for testing many:

* URLs
* paths
* ports
* parameters
* payloads

## subprocess

```python
import subprocess

result = subprocess.run(
    ["whoami"],
    capture_output=True,
    text=True
)

print(result.stdout)
```

Python can interact with operating-system commands.

Be careful when passing user-controlled input to system commands.

## Regex

Regex can be used to extract patterns from text.

```python
import re

match = re.search(r"User ID: (\d+)", text)

if match:
    print(match.group(1))
```

## Security Automation

Example workflow:

```text
Wordlist
   ↓
Loop
   ↓
Build request
   ↓
Send HTTP request
   ↓
Analyze response
   ↓
Print interesting results
```

Regex = Regular Expression.

It is used for pattern matching and extracting structured data from text.

I don't need to master Regex.

I need to be able to read simple patterns and use them when analyzing responses or extracting data.

### Important Patterns

```text
\d       → digit
\w       → word character
\s       → whitespace
.        → any character
+        → one or more
*        → zero or more
?        → zero or one
[abc]    → a, b, or c
[0-9]    → digit from 0 to 9
()       → group
^        → beginning
$        → end
````

### Example

```python
import re

text = "User ID: 12345"

match = re.search(r"User ID: (\d+)", text)

if match:
    print(match.group(1))
```

The regex:

```text
User ID: (\d+)
```

means:

```text
User ID:
   ↓
one or more digits
```

### `search()`

Find a pattern anywhere in the text.

```python
re.search(r"admin", text)
```

### `findall()`

Find all matches.

```python
urls = re.findall(r"https?://\S+", text)
```

### Security Use Cases

Regex can help extract or detect:

```text
URLs
IDs
emails
tokens
specific response patterns
interesting strings
```

Example:

```python
if re.search(r"admin", response.text):
    print("admin found")
```

Security mindset:

```text
Response
   ↓
Regex pattern
   ↓
Find interesting data
   ↓
Analyze it
```

I don't need to memorize Regex.

I need to understand simple patterns and know how to look up more complex ones when needed.



## Pentester Mindset

Turn hypotheses into small scripts.

Example:

Hypothesis:

"Changing a parameter changes the server response."

```text
Parameter values
      ↓
HTTP requests
      ↓
Responses
      ↓
Compare
```

The goal is not to replace existing tools.

The goal is to understand their underlying process.
