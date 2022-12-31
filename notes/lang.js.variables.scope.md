---
id: 0k08ua06s9i46tq4s14gtj6
title: Scope
desc: ''
updated: 1670876036933
created: 1666550109328
---

## Variable Scope
- Scoping refers to how the variables are organized and accessed
- Javascript uses **lexical scoping** i.e scoping controlled based on placement of functions and blocks in the code
- Scope: Space / Environment in which a certain variable is declared. There is **global**, **function** and **block** scope. 
- The scope of a variable refers to the region of our code where a certain variable can be **accessed**

<br>

### The **global** scope
- outside of any function or block
- variables declared in global scope are accessible everywhere

```js
const me = `Jonas`;
const job = `teacher`;
const year = 1989;
```

### The **function** scope
- variables accessible only inside function and NOT outside
- Also called local scope

```js
function calcAge(birthYear) {
  const now = 2037;
  const age = now - birthYear;
  return age;
}
console.log(now); // ReferenceError
```
<small>(from ES6 onwards)</small> 
### The **block** scope

- Variables accessible only inside block i.e. within curly braces `{}`
- However, only applies to **`let`** and **`const`** variables. **`var`** is function scoped
- Functions are also block scoped (only in **STRICT** mode)
  
```js
if (year >= 1381 && year <= 1996) {
    const millenial = true;
    const food = `Avocado toast`
}
console.log(millenial); // ReferenceError
```

### The scope chain 

```js
// global scope starts --------------------------------------------------|
  const myName = `Jonas`

  function first() {
  // function first() scope starts ----------------------------------| 
      const age = 30;

      if (age>=30) {
      // block scope starts ------------------------------------|  
          const decade = 3;
          var millenial = true;
      // block scope ends --------------------------------------|      
      }
      
      function second() {
      // function second() scope starts ------------------------|  
          const job = `teacher`;
          console.log(`${myName} is a ${age} years old ${job}`);
          // Jonas is a 30 years old teacher
      // function second() scope ends --------------------------|      
      }
      second()
  // function first() scope ends ------------------------------------|     
  }
  first()
// global scope ends ----------------------------------------------------|  
``` 
#### Scopes look up the scope chain to get the variable they need
- Global Scope
  - `myName = "Jonas"`
- `first()` scope
  - `age = 30`
  - `millenial = true`
  - other accessible variables within scope: `myName`
- `second()` scope
  - `job = "teacher"`
  - other accessible variables within scope: `age`, `millenial`, `myName`
- `if` block scope
  - `decade = 3`
  - other accessible variables within scope: `age`, `millenial`, `myName`
- Note that the `if` block and `second()` do not have each other's variables

### Scoping in Practice

```js
'use strict';
// global scope starts
function calcAge(birthYear) {
    
  const age = 2037 - birthYear;

  function printAge() {
    // firstName still accessible here despite having coded later
    // Why? The code in the function only executed once it's actually called
    let output = `${firstName}, you are ${age}, born in ${birthYear}`;
    console.log(output);

    if (birthYear >= 1981 && birthYear <= 1996) {
      var millenial = true;

      // Creating NEW variable with same name as outer scope's variable
      const firstName = 'Steven';

      // Reasssigning outer scope's variable
      output = 'NEW OUTPUT!';
      const str = `Oh, and you're a millenial, ${firstName}`;

      console.log(str);

      function add(a, b) {
        return a + b;
      }
    }
    // console.log(str);
    console.log(millenial);
    // console.log(add(2, 3));
    console.log(output);
  }
  printAge();
  return age;
}

const firstName = 'Jonas';
calcAge(1991);
// console.log(age);
// printAge(); // Will fail since 
```