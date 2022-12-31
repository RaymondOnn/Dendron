---
id: coe7ka6k8dm1h40s24p73hq
title: Variables
desc: ''
updated: 1666550125408
created: 1666466190206
---

## Naming Conventions for Variables ###
- variable names generally in camelcase for Javascript
- variable names cannot start with numbers
- can only contain numbers, letters, underscore or dollar sign
- Javascript reserved keywords are not allowed e.g. New
- never start with uppercase letters
- variable names that have all uppercase letters are reserved for constants that never change
- recommended that variable names are descriptive to avoid confusion

<br>

## Declaring Variables ##

**`let`** 
- variable will be re-assigned at a later point i.e. want to mutate the variable `let age = 30;`

**`const`**
- variable not meant to be re-assigned, 
- When assigned a value of the primitive data types, it cannot be changed. 
- Hence, undefined variable using `const` are not possible. `const birthYear = 1991;`
- Arrays assigned to variables using `const` can still be mutated / changed, but not re-assigned.

**`var`**
- similar to `let`. 
- However, this method is outdated and should not be useful `var job = 'programmer'`

While possible to create variables without using the keywords, it is not recommended to do so as it does not create the variable in the current scope but on the global object.


