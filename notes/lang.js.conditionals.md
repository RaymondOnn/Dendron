---
id: bb1fr6yuv8ylolfaikz6val
title: conditionals
desc: ''
updated: 1697907855055
created: 1666466609426
---

## Statements and Expressions
- Expressions are pieces of code that returns a value
- Statements do not return values by themselves
  
```js
// These are expressions
3 + 4
1991
true && false && !false

// These are statements
if (23 > 10) {
  const str = '23 is bigger';
}

// PlaceHolders only allow expressions and not statements!!
const me = 'Jonas';
console.log(`I'm ${2037 - 1991} years old ${me}`);
```

### `if-else` Statements ##
- Note: no `elseif` concept in `javascript`
```js

const age = 19;
const isOldEnough = age >= 18;

if(isOldEnough) {
    console.log('Sarah can start driving license')
} else {
    const yearLeft = 18 - age;
    console.log(`Sarah is too young. Wait another ${yearLeft} years :)`)
}

const birthYear = 1998;
let cebtury;
if(birthYear <= 2000) {
    century = 20;
} else {
    century = 21;
}
console.log(century);

```
---

### `switch` Statement ##

```js
const day = 'monday';

const day = 'friday';
switch (day) {
  case 'monday': // day === 'monday'
    console.log('Plan course structure');
    console.log('Go to coding meetup');
    break;
  case 'tuesday':
    console.log('Prepare theory videos');
    break;
  case 'wednesday':
  case 'thursday':
    console.log('Write code examples');
    break;
  case 'friday':
    console.log('Record videos');
    break;
  case 'saturday':
  case 'sunday':
    console.log('Enjoy the weekend :D');
    break;
  default:
    console.log('Not a valid day!');
}


//if else equivalent
if (day === 'monday') {
  console.log('Plan course structure');
  console.log('Go to coding meetup');
} else if (day === 'tuesday') {
  console.log('Prepare theory videos');
} else if (day === 'wednesday' || day === 'thursday') {
  console.log('Write code examples');
} else if (day === 'friday') {
  console.log('Record videos');
} else if (day === 'saturday' || day === 'sunday') {
  console.log('Enjoy the weekend :D');
} else {
  console.log('Not a valid day!');
}
```

