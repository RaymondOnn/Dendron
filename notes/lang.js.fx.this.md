---
id: 4297m6bjzz3fq9ro8e10dhn
title: This
desc: ''
updated: 1672440738547
created: 1670880848365
---


### The **`this`** Keyword in Practice
- Special variable created for every execution context
- Takes the value of the 'owner' of the function in which the `this` keyword is used
- its value is not static and depends on how the function is called
- the value is only assigned when function is called
- **does not point to the function itself or its variable environment**
- Alternatives: `new`, `call`, `apply`, `bind`



| Type                      | Hoisted?                                                               |
| ------------------------- | ---------------------------------------------------------------------- |
| Method (e.g. `this.year`) | `this` = &lt; Object that is calling the method &gt;                   |
| Simple function call      | `this` = undefined (if strict mode, else window object in the browser) |
| Arrow functions           | `this` = &lt; `this` of surrounding function &gt;                      |
| Event Listener            | `this` = &lt; DOM element that the handler is attached to &gt;         |




```js
// Within Global Scope
console.log(this); // Returns: Window object

// Simple Function Call example
const calcAge = function (birthYear) {
  console.log(2037 - birthYear);
  console.log(this); // Returns: undefined (in strict mode)
};


// Arrow Function example
calcAge(1991);
const calcAgeArrow = birthYear => {
  console.log(2037 - birthYear);
  console.log(this); // Results: Window Object since it's the this keyword of parent scope
};
calcAgeArrow(1980);


// Method example
const jonas = {
  year: 1991,
  calcAge: function () {
    console.log(this);
    console.log(2037 - this.year); // Results: Jonas Object
  },
};
jonas.calcAge();


const matilda = {
  year: 2017,
};
matilda.calcAge = jonas.calcAge; // This is method borrowing
matilda.calcAge(); // this keyword involved here points to 'matilda' object
 
const f = jonas.calcAge;
f(); // returns undefined since this is a regular function call
```