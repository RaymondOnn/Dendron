---
id: 7n40yaon0gx1p288bf30pdn
title: conditionals
desc: ''
updated: 1706539650774
created: 1706538940028
---

## Conditionals

-   General format:
    ```makefile
    conditional-directive-one
    text-if-one-is-true
    else conditional-directive-two
    text-if-two-is-true
    else
    text-if-one-and-two-are-false
    endif
    ```
    -   There can be as many “else conditional-directive” clauses as necessary.
    -   Once a given condition is true, text-if-true is used and no other clause is used
    -   if no condition is true then text-if-false is used.
    -   The text-if-true and text-if-false can be any number of lines of text.

### Using Conditionals

```makefile
libs_for_gcc = -lgnu
normal_libs =

foo: $(objects)
ifeq ($(CC),gcc)
    $(CC) -o foo $(objects) $(libs_for_gcc)
else
    $(CC) -o foo $(objects) $(normal_libs)
endif
```

### Testing conditions

-   `ifeq (arg1, arg2)`: 
    -   Expand all variable references in arg1 and arg2 and compare them. If they are identical, the text-if-true is effective; otherwise, the text-if-false, if any, is effective.
-   `ifneq (arg1, arg2)`: 
    -   Expand all variable references in arg1 and arg2 and compare them. If they are different, the text-if-true is effective; otherwise, the text-if-false, if any, is effective.
-   `ifdef variable-name`: 
    -   The ifdef form takes the name of a variable as its argument, not a reference to a variable. If the value of that variable has a non-empty value, the text-if-true is effective; otherwise, the text-if-false, if any, is effective. Variables that have never been defined have an empty value.
-   `ifndef variable-name`: 
    -   If the variable variable-name has an empty value, the text-if-true is effective; otherwise, the text-if-false, if any, is effective. The rules for expansion and testing of variable-name are identical to the ifdef directive.
