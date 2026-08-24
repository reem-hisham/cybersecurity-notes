# Lab: Reflected DOM XSS

## Goal

Exploit a reflected DOM XSS vulnerability to execute `alert()`.

## What I Observed

The vulnerable functionality was the **search bar**.

The application:

- Reflected my search input into the search results response.
- Returned the search results as JSON-like data.
- The client-side JavaScript retrieved this response.
- The response was passed directly to `eval()`.

Vulnerable pattern:

```js
eval('var searchResultsObj = ' + this.responseText);
````

The important problem is that `responseText` contains attacker-controlled data
and is being evaluated as JavaScript.

## Exploitation

I used:

```text
Shopping\"};alert(1)//
```

The payload breaks out of the expected string/object structure, injects:

```js
alert(1)
```

and uses:

```text
//
```

to comment out the remaining JavaScript.

## Attack Flow

```text
Search input
    ↓
Server reflects input into response
    ↓
responseText
    ↓
eval()
    ↓
Input becomes JavaScript code
    ↓
alert(1)
```

## Key Takeaways

* Reflected DOM XSS can involve both server-side reflection and
  client-side JavaScript.
* `eval()` is dangerous when used with attacker-controlled data because it
  evaluates the supplied string as JavaScript.
* JSON data should be parsed as data, not executed as JavaScript.
* When analyzing DOM XSS, look for data flowing from a source into a
  dangerous sink such as `eval()`.
* Breaking out of the expected data structure can turn reflected data into
  executable JavaScript.

**Lab solved.**
