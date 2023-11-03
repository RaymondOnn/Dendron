---
id: 588n47tcyurufsqf6d19v7y
title: recursion
desc: ''
updated: 1697311761759
created: 1697311583691
---


## Recursion♻️

### What is Recursion

- Recursion is when a function keeps calling itself until it accomplishes its main goal. Functions that do this are called recursive functions.
- Recursive functions do not use for or while loops as they prioritize recursion over iteration.

### Examples

#### Iterative function🚫

- An iterative function (or iterator) loops through a sequence of elements and applies an operation to each element within the sequence.

```py
def multiply_numbers(numbers):
    total = 1
    for number in numbers:
        total *= number
    return total

list_of_numbers = [1, 2, 3]
print(multiply_numbers(list_of_numbers))
>>> 6
```

This function doesn’t qualify as a recursive function because it uses a for loop to achieve the multiplication operation.

#### Recursive function

```py
def multiply_numbers(numbers):
    if len(numbers) == 1:
        return numbers[0]
    else:
        return numbers[0] * multiply_numbers(numbers[1:])

list_of_numbers = [1, 2, 3]
print(multiply_numbers(list_of_numbers))
>>> 6
```

### Iterations vs Recursion

- However, there is a cost to using recursions: we trade fast performance for simple and readable functions in many scenarios.
- Like any trade-off, you should perform a cost-benefit analysis to determine whether this is worth it for your distinctive use cases.
- So use:
  - recursions for easy-to-read functions
  - iterations for speedy functions
