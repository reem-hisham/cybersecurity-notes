# Lab: DOM XSS in AngularJS Expression

## Goal

Exploit an AngularJS expression to execute JavaScript.

The application HTML-encoded angle brackets and double quotes, so the usual
HTML/JavaScript injection techniques were not useful.

The vulnerability was in an AngularJS expression that processed user input.

---

## AngularJS Basics I Learned

### `ng-app`

Defines the AngularJS application on the page.

```html
<body ng-app="myApp">
````

It tells AngularJS where the application starts.

### `ng-controller`

Connects part of the page to a controller.

```html
<body ng-app="myApp" ng-controller="myCtrl">
```

### `$scope`

`$scope` is the object used to expose data and functions to the AngularJS
view.

```js
app.controller('myCtrl', function($scope) {
    $scope.name = "Gentil Security";
});
```

The view can then access:

```html
{{name}}
```

### `{{ }}` — AngularJS Interpolation

AngularJS uses `{{ }}` to evaluate expressions.

Example:

```html
{{name}}
```

The important point is that AngularJS expressions are **not the same as
normal JavaScript code**. They are evaluated in AngularJS's expression
context.

---

## Important JavaScript Concepts

### Immediately Invoked Function Expression (IIFE)

A function can be created and immediately executed:

```js
(function() {
    alert(1);
})();
```

The final `()` calls the function immediately.

### Function Constructor

JavaScript functions have a `constructor` property that can provide access
to the `Function` constructor.

Conceptually:

```js
someFunction.constructor(...)
```

can create a new function from a string of JavaScript code.

For example:

```js
Function('alert(1)')()
```

The first `()` creates the function and the second `()` executes it.

---

## Exploitation

The final payload was:

```text
{{ $on.constructor('alert()')() }}
```

The important chain is:

```text
{{ }}
   ↓
AngularJS evaluates expression
   ↓
$on
   ↓
constructor
   ↓
Function constructor
   ↓
Creates function containing alert()
   ↓
()
   ↓
Function executes
```

### Why `$on`?

`$on` is an AngularJS `$scope` method used for registering event listeners.

Because it is a function, JavaScript's `constructor` property can be reached
from it.

The payload therefore abuses:

```text
$on
 ↓
constructor
 ↓
Function
```

to escape the limitations of the AngularJS expression and create executable
JavaScript.

---

## Why HTML Encoding Didn't Stop the Attack

The application encoded characters such as:

```text
<
>
"
```

This prevented common HTML-based payloads.

However, the exploit did not require HTML tags.

It used an **AngularJS expression**:

```text
{{ ... }}
```

and abused JavaScript functionality available through the expression context.

---

## Key Takeaways

* AngularJS uses `{{ }}` for expression interpolation.
* `ng-app` defines the AngularJS application.
* `ng-controller` connects a controller to the view.
* `$scope` exposes data and functions to the AngularJS view.
* `$on` is a scope event-listener function.
* JavaScript functions have a `constructor` property.
* The `Function` constructor can create executable functions from strings.
* AngularJS expression restrictions can sometimes be bypassed by reaching
  JavaScript functionality through accessible objects/functions.
* HTML encoding does not necessarily prevent DOM XSS if the dangerous
  interpretation happens inside a JavaScript framework.

## Exploit Chain

```text
User input
    ↓
AngularJS interpolation {{ }}
    ↓
AngularJS expression evaluation
    ↓
$on function
    ↓
.constructor
    ↓
Function constructor
    ↓
Create JavaScript function
    ↓
()
    ↓
JavaScript execution
```

**Lab solved.**

````

### The most important thing to remember

If you come back to this note a year from now, the one line I want you to immediately understand is:

```text
{{ $on.constructor('alert()')() }}
        │        │       │
        │        │       └── execute the created function
        │        └────────── create a function using Function constructor
        └────────────────── AngularJS scope function
````
