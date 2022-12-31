---
id: hgek8kyxlcsatol2i1twty1
title: Dtypes
desc: ''
updated: 1666552002038
created: 1666550029626
---
## Data Types ##

Primitive data types 
- Number: for decimals and integers  <code> let age = 23 </code>
- String: for text <code> let firstName = 'Jonas';</code>
- Boolean: for decision making <code> let fullAge = true; </code>>
- Undefined: value taken by a variable that is not yet defined ('empty value') <code> let children; </code>
- Null: Also means empty value
- Symbol: Value that is unique and cannot be changed
- BigInt: Larger integers than the Number type can hold

Note: JavaScript has dynamic typing i.e. we do not have to manually define the data type of the value stored in a variable. Instead, data types are determined automatically -> Value has type, NOT variable!!


### Getting the data type ###

```js
let javascriptIsFun = true;
console.log(javascriptIsFun);

// To get the current data type, we can use the typeof function
console.log(typeof true);
console.log(typeof javascriptIsFun);

console.log(typeof true);
console.log(typeof 23);
console.log(typeof 'Jonas');
```

### Dynamic Typing ###
```javascript
// Seeing Dynamic Typing in action!!
let javascriptIsFun = true;
console.log(javascriptIsFun);
console.log(typeof javascriptIsFun);

javascriptIsFun = 'YES!'
console.log(typeof javascriptIsFun);
```
### Undefined Data Type ###
```javascript
// both value and data type are undefined
let year;
console.log(year);
console.log(typeof year);

// When we assign a value to the value, it no longer is undefined
year = 1991;
console.log(typeof year);
```

## Type Conversion and Coercion ##

```js
// Type Conversion
const inputYear = '1991'
console.log(Number(inputYear), inputYear);
console.log(inputYear + 18);

//returns NaN; an invalid number
console.log(Number('Jonas'));
console.log(typeof NaN);

console.log(String(23), 23);

// Type Coercion: Conversion occurs in the background automatically
console.log('I am ' + 23 + ' years old')
console.log('23' - '10' - 3)
console.log('23' + '10' + 3)
console.log('23' * '2')
```

Reference vs Primitive Data Types
