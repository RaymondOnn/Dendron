---
id: ijjg604t597mn75zi2gg3jx
title: Hoisting
desc: ''
updated: 1670880793743
created: 1670880793743
---


### Hoisting and TDZ (Temporal Death Zone) in Practice
- Before execution, code scanned for variable declarations
- For each variable, a new property is created in the **variable environment object**

| Type                                   | Hoisted?                           | Initial Value     | Scope    |
| -------------------------------------- | ---------------------------------- | ----------------- | -------- |
| function declarations                  | YES                                | Actual function   | Block    |
| `var` variables                        | YES                                | undefined         | Function |
| `let`/`const` variables                | NO                                 | unintialized, TDZ | Block    |
| function expressions / arrow functions | Depends  if using var or let/const |


```js
// Variables
console.log(me);
console.log(job);
console.log(year);

var me = 'Jonas'; // undefined error
let job = 'teacher'; // 'Cannot access before initialization'
const year = 1991; // 'Cannot access before initialization'

// Note: 
// - undefined: variable exist but not assigned a value
// - uninitialized: variable does not exist
```

```js
// Functions
console.log(addDecl(2, 3));  // returns 5
console.log(addExpr(2, 3)); // uninitialized
console.log(addArrow(2, 3)); // uninitialized

function addDecl(a, b) {
  return a + b;
}
const addExpr = function (a, b) {
  return a + b;
};

const addArrow = (a, b) => a + b;

console.log(addExpr(2, 3)); // 'addExpr not a function' since addExpr = undefined 
console.log(addArrow); 

var addExpr = function (a, b) {
  return a + b;
};

var addArrow = (a, b) => a + b;
```
### A pitfall of Hoisting...

```js
// Example: Why we shouldn't use var
console.log(undefined);

if (!numProducts) deleteShoppingCart();
var numProducts = 10;
function deleteShoppingCart() {
  console.log('All products deleted!');
}
// Result: 'All products deleted!' since both 0 and undefined are falsy values
```

```js
// window is the global object of javascript in the browser
// only variables declared using var will create a property in the window object
var x = 1;
let y = 2;
const z = 3;
console.log(x === window.x);
console.log(y === window.y);
console.log(z === window.z);
```