---
id: vwgpi1r113v9zfe3x17p34b
title: monoids
desc: ''
updated: 1697313351305
created: 1697310310652
---
## Monoids

### What is a monoid

- A monoid is anything that consists of a binary operation and an identity element.
- From a functional programming perspective, a monoid is a set of values that are concatenated at any point in time while satisfying these properties:
  - the output shares the same type as its input arguments (closure)
  - a neutral element that doesn’t change the answer when combined with other input arguments is present (identity)
  - the elements can be ordered in any way and still return the same answer (associativity)

- Irrespective of the internal operations in the function, as long as the input argument shares the same type as the result, they qualify as a monoid.
- A monoid’s main task is combining data of the same type like integers, strings, lists etc.
- For example, a function that takes in currency in $ and returns currency in $ would be an example of a monoid.

### Properties of Monoids

- Identity - you add your set of values to an empty value or string and the results remain the same
- Associativity - you can order the values however you like, but you will still get the same answer

#### Closure

- Closure refers to the binary operation’s ability to produce an output that is the same type as the input arguments of a function.
  - A binary operation can be addition, multiplication, subtraction, division, or any other operation that involves at least two different variables combined to form another variable.
  - In other words, a function qualifies as a monoid if its combination operation creates a result that shares the same type as its inputs.
- you take type X and return an output type of X; the input’s type is the same as the output’s type

Mathematic example:

``` raw
10 / 8 = 1.25
Not monoids: involves an integer dividing another integer but the result is a float type

2 + 2 = 4
Is monoid: An integer plus an integer results in an integer
```

<br>

Code example:

``` py
# Not a monoid: could have a float and an int as the inputs and having a float as output.
def add_items(a, b):
    return a + b

result = add_items(10.25, 20)
print(result)
>>> 30.25

# Is monoid: The add_items only accepts and returns integers, and throws an exception otherwise
def add_items(a: int, b: int) -> int:
    if not isinstance(a, int) or not isinstance(b, int):
        raise TypeError("Only integers can be inserted into the function")
    return a + b

result = add_items(10.25, 20)
print(result)
>>> Traceback (most recent call last):
>>> File "<string>", line 6, in <module>
>>> File "<string>", line 3, in add_items
>>> TypeError: Only integers can be inserted into the function
```

#### Identity

- This states that something must be added to the arguments of a function that makes it still return the same output. In a short sentence, a monoid must have an identity element.
- An identity element (or neutral element) is a value combined with other elements passed into the function's arguments that keeps the output value the same. So in an equation like 3 * 1 = 3, the value 1 is the identity element.

>Note:
>
> - The identity element depends on the binary operation selected for the monoid.
> - So although 1 is the identity element in this multiplication example, the same use of numbers will fail to create a monoid in an addition operation because 3 + 1 does not equal 3 - to make this a monoid, 0 would need to replace 1 in this instance.

``` raw
10 + 0 = 10
The identity element here is 0, because the result of 10 remains unchanged

"Hello Sam" + "" = "Hello Sam"
The empty double quotes used here are the identity element as the output of the operation is still "Hello Sam"
```

``` py
# This concatenation operation uses the empty square brackets as the identity element and the results remain the same.
[2, 4, 6] + [] = [2, 4, 6]
```

#### Associativity

- This states that the monoid must be associative.
- Associative means the results will return the **same answer no matter how you order the values in the operation**.
- For example, (2 + 3) + 4 is the same as 2 + (3 + 4), so addition in this context is associative and qualifies as a monoid.

``` py
def multiply_items(x, y):
    return x * y

multiply_numbers_1 = multiply_items(multiply_items(3, 4), 5)     # (3 *4)* 5
multiply_numbers_2 = multiply_items(3, multiply_items(4, 5))     # 3 *(4* 5)

print(f'1st approach: {multiply_numbers_1}')
print(f'2nd approach: {multiply_numbers_2}')
>>> 1st approach: 60
>>> 2nd approach: 60
```

This code multiplies two arguments passed into the multiply_items function. By creating two separate approaches that multiply the numbers 3, 4 and 5 in different orders, we demonstrate how this meets the associativity rule for monoids.

Like Scott Wlaschin often says in his talks, once you understand monoids you begin to see them everywhere!
