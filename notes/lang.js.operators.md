---
id: ikppnxj21dyua8672yzl4eq
title: Operators
desc: ''
updated: 1666467316416
created: 1666465798015
---

## Operators ##

```js
// Math Operators
const now = 2037;
const ageJonas = now - 1991;
const ageSarah - now - 2018;
console.log(ageJonas, ageSarah)

// 2 ** 3 is 2 to the power of 3
console.log(ageJonas * 2, ageJonas / 10, 2 ** 3);

// String Operators
const firstName = 'Jonas';
const lastName = 'Schmedtmann';
console.log(firstName + " " + lastName);

// Assignment Operators
let x = 10 + 5
x += 10; // x = x + 10
x *= 4; // x = x * 4
x ++; // x = x + 1
console.log(x);

// Comparison Operators
console.log(ageJonas > ageSarah);
console.log(ageSarah >= 18);

```
## Operator Precedence ## 
based on: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence#table


```js
let x, y
x = y = 25 - 10 - 5 // x = y = 10; y = 10; x = 10
console.log(x, y)

const now = 2037;
onst ageJonas = now - 1991;
const ageSarah - now - 2018;
const averageAge = (ageJonas + ageSarah) / 2
console.log(ageJonas, ageSarah, averageAge)
```

## Equality Operators: == vs. === ## 

  Overall, it's a good practice to just use the **STRICT** version by default as the other version might introduce bugs
```js
const age = '18';
// if if-else statement is only one line, curly braces are optional
// === is the STRICT equality operator as it does not do type coercion.
if (age === 18) console.log('You just became an adult(strict)');

// == is the LOOSE EQUALITY operator as it does type coercion.
if (age == 18) console.log('You just became an adult (loose)');
```

```js
if (favourite === 23) { // 22 === 23 -> FALSE
  console.log('Cool! 23 is an amzaing number!')
} else if (favourite === 7) {
  console.log('7 is also a cool number')
} else if (favourite === 9) {
  console.log('9 is also a cool number')
} else {
  console.log('Number is not 23 or 7 or 9')
}
if (favourite !== 23) console.log('Why not 23?');
```


## Logical Operators: `&&, ||`
```js
const hasDriversLicense = true; // A
const hasGoodVision = true; // B
console.log(hasDriversLicense && hasGoodVision);
console.log(hasDriversLicense || hasGoodVision);
console.log(!hasDriversLicense);
// if (hasDriversLicense && hasGoodVision) {
//   console.log('Sarah is able to drive!');
// } else {
//   console.log('Someone else should drive...');
// }
const isTired = false; // C
console.log(hasDriversLicense && hasGoodVision && isTired);
if (hasDriversLicense && hasGoodVision && !isTired) {
  console.log('Sarah is able to drive!');
} else {
  console.log('Someone else should drive...');
}
```

## The Conditional (Ternary) Operator
`condition ? exprIfTrue : exprIfFalse`
```js
const age = 23;
// age >= 18 ? console.log('I like to drink wine 🍷') : console.log('I like to drink water 💧');

// if age >= 18 then drink = wine else drink = water
const drink = age >= 18 ? 'wine 🍷' : 'water 💧';
console.log(drink);

let drink2;
if (age >= 18) {
  drink2 = 'wine 🍷';
} else {
  drink2 = 'water 💧';
}
console.log(drink2);
console.log(`I like to drink ${age >= 18 ? 'wine 🍷' : 'water 💧'}`);
```
