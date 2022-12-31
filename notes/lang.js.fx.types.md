---
id: kvxtjywt9yozcqn1ejvr9z7
title: Types
desc: ''
updated: 1670880965884
created: 1670880965884
---


## Functions
- In javascript, functions are first-class and are simpy treated as variables.
- Hence, we can pass them into other functions and return them from functions

``` js
function logger() {
  console.log('My name is Jonas');
}
// calling / running / invoking function
logger();
logger();
logger();

function fruitProcessor(apples, oranges) {
  const juice = `Juice with ${apples} apples and ${oranges} oranges.`;
  return juice;
}
const appleJuice = fruitProcessor(5, 0);
console.log(appleJuice);

const appleOrangeJuice = fruitProcessor(2, 4);
console.log(appleOrangeJuice);
const num = Number('23');
```


## Function Declarations vs. Expressions
  - Functions are actually just values, and can also be stored in a variable
  - For function declarations, functions can be called before they are defined due to hoisting i.e. the declaration code get pushed to the top and run first
  - Function expressions are executed once they are defined which is useful for preventing conflicting with variables in the global scope

```js
// Function declaration
// we have a function, calcAge1 here
function calcAge1(birthYeah) {
  return 2037 - birthYeah;
}
const age1 = calcAge1(1991);

// Function expression
// we have "some" function that we stored in the variable, calcAge2
const calcAge2 = function (birthYeah) {
  return 2037 - birthYeah;
}
const age2 = calcAge2(1991);
console.log(age1, age2);
```


```js

var runIt = function(a) {
  console.log(a);
  a();
}
runIt(function() {
  var b = " Now";
  console.log("Running" + b);
})
```


## Arrow functions
- Note: Arrow functions do not get a `THIS` keyword
  
```js
// return is implictly assumed
const calcAge3 = birthYeah => 2037 - birthYeah;
const age3 = calcAge3(1991);
console.log(age3);

const yearsUntilRetirement = (birthYeah, firstName) => {
  const age = 2037 - birthYeah;
  const retirement = 65 - age;
  // return retirement;
  return `${firstName} retires in ${retirement} years`;
}
console.log(yearsUntilRetirement(1991, 'Jonas')); console.log(yearsUntilRetirement(1980, 'Bob'));
```

```js
// Convert function declaration into arrow functions
// the function sum returns the value of (a + b)
function sum(a, b) {
  return a + b
}
// Arrow function equivalent
// the variable sum has a value of (a + b)
let sum = (a, b) => {a + b}

## Functions Calling Other Functions

```js
function cutFruitPieces(fruit) {
  return fruit * 4;
}
function fruitProcessor(apples, oranges) {
  const applePieces = cutFruitPieces(apples);
  const orangePieces = cutFruitPieces(oranges);
  const juice = `Juice with ${applePieces} piece of apple and ${orangePieces} pieces of orange.`;
  return juice;
}
console.log(fruitProcessor(2, 3));
```

## Reviewing Functions

```js
const calcAge = function (birthYeah) {
  return 2037 - birthYeah;
}
const yearsUntilRetirement = function (birthYeah, firstName) {
  const age = calcAge(birthYeah);
  const retirement = 65 - age;
  if (retirement > 0) {
    console.log(`${firstName} retires in ${retirement} years`);
    return retirement;
  } else {
    console.log(`${firstName} has already retired 🎉`);
    return -1;
  }
}
console.log(yearsUntilRetirement(1991, 'Jonas'));
console.log(yearsUntilRetirement(1950, 'Mike'));
*/
```