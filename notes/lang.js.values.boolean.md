---
id: evf22mfhrp55zd1f5xpiteh
title: Boolean
desc: ''
updated: 1666465762808
created: 1666465728654
---

## Truthy and Falsy Values

Falsy Values: Becomes false when converted to Boolean type
- 0
- ''
- undefined
- null
- NaN

```js
console.log(Boolean(0));
console.log(Boolean(undefined));
console.log(Boolean('Jonas');
console.log(Boolean({}));
console.log(Boolean(''));

const money = 0;
if (money) {
    console.log("Don't spend it all ;)");
} else {
    console.log("You should get a job!");
}

// what id height = 0?
let height;
if (height) {
    console.log("YAY! Height is defined");
} else {
    console.log("Height is UNDEFINED!");
}
```