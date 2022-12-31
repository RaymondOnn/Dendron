---
id: ewb3do31mlfbhq0p7drnu9i
title: Default_params
desc: ''
updated: 1670880904828
created: 1670880904828
---


## Default Parameters
### Before ES6

<br>

- Method 1
```js
function myfunc(a, b)
{
    // use this if you specifically want to know if b was passed
    if (b === undefined) {
        // b was not passed
    }
    // use this if you know that a truthy value comparison will be enough
    if (b) {
        // b was passed and has truthy value
    } else {
        // b was not passed or has falsy value
    }
    // use this to set b to a default value (using truthy comparison)
    b = b || "default value";
}
```
Here, the expression `b || "default value"` evaluates the value AND existence of b and returns the value of "default value" if b either doesn't exist or is falsy.


- Method 2
```js
function myfunc(a)
{
    var b;

    // use this to determine whether b was passed or not
    if (arguments.length == 1) {
        // b was not passed
    } else {
        b = arguments[1]; // take second argument
    }
}
```
The special "array" arguments is available inside the function; it contains all the arguments, starting from index 0 to N - 1 (where N is the number of arguments passed).

This is typically used to support an unknown number of optional parameters (of the same type); however, stating the expected arguments is preferred!

#### Other possible ways to check if b was passed
  - `b === void 0;`
  - using `typeof b === 'undefined' ? default_value : exprIfFalse`  which also works for undeclared variables

<br>

### After ES6
```js
function myfunc(a, b = "default_value")
{
  // do something
}
```