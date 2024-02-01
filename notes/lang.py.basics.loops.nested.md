---
id: 6n8482du1tkc2vps4lnr4yf
title: nested
desc: ''
updated: 1697833958034
created: 1697832762587
---

## Alternatives to Nested Loops

### Flattening with itertools.product()

- Useful for performing operations on all combinations of the loop variables.
- More concise and readable.

``` py
colors = ['red', 'green', 'blue']
sizes = ['small', 'medium', 'large']
shapes = ['circle', 'square', 'triangle', 'hexagon']

for color in colors:
    for size in sizes:
        for shape in shapes:
            print(color, size, shape)



# Alternative: itertools.product() function. 
import itertools

colors = ['red', 'green', 'blue']
sizes = ['small', 'medium', 'large']
shapes = ['circle', 'square', 'triangle', 'hexagon']

# Instead of 3 loops, we now only need 1 loop:
for color, size, shape in itertools.product(colors, sizes, shapes):
    print(color, size, shape)
```

### Flattening with itertools.chain()

- If we have multiples variables and we want to iterate over them sequentially as if they were a single iterable, we should consider using chain
- More efficient

``` py
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flattened_list = []

for row in matrix:
    for element in row:
        flattened_list.append(element)

print(flattened_list)  
>>> Output: [1, 2, 3, 4, 5, 6, 7, 8, 9]



# Alternative: itertools.chain() .
import itertools

matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# * operator unpacks the elements 
# itertools.chain() function concatenates the individual elements into a single iterator
flattened_list = list(itertools.chain(*matrix))

print(flattened_list)  
>>> Output: [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### Flattening with Comprehensions

- Keeps our code shorter and more concise.
- Recommended to refactor your code with a one-liner if it can improve the readability.

``` py
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

flattened_list = [element for row in matrix for element in row]

print(flattened_list)  
>>> Output: [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### Consider Alternative Data Structures or Algorithms
Sometimes we can avoid nested loops by thinking outside the loop. For example, we can preprocess the data before entering the nested loop. This can help reduce the complexity of the nested loops by simplifying the data or deriving intermediate results in advance.

``` py
# Example: Find a pair of numbers that can be added to 12

items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
target_sum = 12

# Nested loop approach
pairs = []
for i in range(len(items)):
    for j in range(i + 1, len(items)):
        if items[i] + items[j] == target_sum:
            pairs.append((items[i], items[j]))

print(pairs)  
>>> [(5, 7), (4, 8), (3, 9), (2, 10)]

# Alternative:
items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
target_sum = 12
pairs = []
complements = set()

for item in items:
    complement = target_sum - item
    if complement in complements:
        pairs.append((item, complement))
    complements.add(item)

print(pairs)  # [(7, 5), (8, 4), (9, 3), (10, 2)]
```


### Chunking Logically Related Lines in Functions
- If the code has deep nested loops and complex if-conditions, chunking logically related lines in functions can be a good approach. 
- By organizing the code into separate functions, we can encapsulate specific logic and make the code more modular.

``` py
# Example: Given two lists, find all pairs of numbers where one number is from the first list and the other number is from the second list. These two must add up to an even number. 

def find_pairs(numbers1, numbers2):
    pairs = []
    for num1 in numbers1:
        for num2 in numbers2:
            if (num1 + num2) % 2 == 0:
                pairs.append((num1, num2))
    return pairs

numbers1 = [1, 2, 3]
numbers2 = [4, 5, 6]

result = find_pairs(numbers1, numbers2)
print(result)  
>>> [(1, 5), (2, 4), (2, 6), (3, 5)]
We can have the same result when we chunk logically related lines in a function:

def find_pairs(numbers1, numbers2):
    pairs = []
    for num1 in numbers1:
        pairs.extend(get_pairs(num1, numbers2))
    return pairs

def get_pairs(num, numbers):
    return [(num, num2) for num2 in numbers if (num + num2) % 2 == 0]

numbers1 = [1, 2, 3]
numbers2 = [4, 5, 6]

result = find_pairs(numbers1, numbers2)
print(result)  
>>> [(1, 5), (2, 4), (2, 6), (3, 5)]

```

- By chunking the nested loop into a separate function get_pairs, the code becomes more modular and readable. 
- The logic for generating pairs is encapsulated in the get_pairs() function, making it easier to understand and maintain. 
- It also allows for code reuse, as the get_pairs() function can be used in other parts of the code if needed. 
- Additionally, it provides the flexibility to modify or extend the behavior of individual functions independently.