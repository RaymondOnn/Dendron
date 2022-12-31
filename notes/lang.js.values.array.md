---
id: tfglsax8y09uy9h9kt9d3rs
title: Array
desc: ''
updated: 1666542144095
created: 1666466885331
---

### Introduction to Arrays
```js
const friend1 = 'Michael';
const friend2 = 'Steven';
const friend3 = 'Peter';
const friends = ['Michael', 'Steven', 'Peter'];
console.log(friends);

// Another way of creating array
const y = new Array(1991, 1984, 2008, 2020);

// Reference elements in the array by indexing
console.log(friends[0]);
console.log(friends[2]);

console.log(friends.length);
console.log(friends[friends.length - 1]);

// Swapping elements in array
friends[2] = 'Jay';
console.log(friends);

// Elements can be of different data types
// Similar to Python lists
const firstName = 'Jonas';
const jonas = [firstName, 'Schmedtmann', 2037 - 1991, 'teacher', friends];
console.log(jonas);
console.log(jonas.length);
```

### Basic Array Operations (Methods)
```js
const friends = ['Michael', 'Steven', 'Peter'];
// Push adds elements to the end of the array and 
// returns the length of the new array
const newLength = friends.push('Jay');
console.log(friends);
console.log(newLength);

// Unshift adds elements to the start of the array and 
// returns the length of the new array
friends.unshift('John');
console.log(friends);

// Pop removes the last element of the array and 
// returns the removed element
friends.pop(); // Last
const popped = friends.pop();
console.log(popped);
console.log(friends);

// Pop removes the first element of the array and 
// returns the removed element
friends.shift(); // First
console.log(friends);

// indexof returns the index of element
console.log(friends.indexOf('Steven'));
console.log(friends.indexOf('Bob'));
friends.push(23);

// includes checks if element is in the array
// includes uses strict equality!!
console.log(friends.includes('Steven'));
console.log(friends.includes('Bob'));
console.log(friends.includes(23));
if (friends.includes('Steven')) {
  console.log('You have a friend called Steven');
}
```