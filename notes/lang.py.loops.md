---
id: oqube9yym46yi2qqrv9vw0v
title: loops
desc: ''
updated: 1697363176692
created: 1697362473679
---


### Alternatives

#### Lambda Function

- The Lambda function is a one-liner alternative to the regular functions.
- It can be defined in a single line of code and thus takes less time and space in our code.

``` py
# Function with one argument ---------------------------------
# Regular function
def multiply_by_2(x):
    return x*2

# lambda function
lambda x: x*2

# Function with 2 or more arguments ------------------------

# Regular function 
def multiple_three_numbers(x,y,z):
    return x*y*z 

# lambda function 
lambda x, y, z: x*y*z
```

#### Map Function

- `map(function, iterable)`
- Map is a built-in function that takes a function and an iterable as arguments and applies the function to each element in the iterable.
- An iterable could be an object which can be iterated, such as — a list, tuple, etc.

``` py
# Example 1: Finding the square of numbers

## Traditional Loop
nums = (1, 3, 4, 5, 6, 7)

def square_numbers(nums):
  squares = []
  for num in nums:
    squares.append(num**2)

## One-liner 
squares = list(map(lambda x: x**2, nums))

# Example 2: Counting the number of words in each sentence. 

## Traditional Loop
sentences = ['This is the first sentence.', 
'And this is the second sentence.', 
'Finally, the third sentence.']

word_counts = []
for sentence in sentences:
    words = sentence.split()
    word_count = len(words)
    word_counts.append(word_count)


### line liner 
word_counts = list(map(lambda sentence: len(sentence.split()), sentences))
```

#### Filter

- `filter(function, iterable)`
- The filter is a built-in function that takes a function and an iterable as arguments and returns a new iterable containing only the elements for which the function returns True.
- It can be used to replace “for” loops that are used to filter out elements from an iterable. Here’s an example:

``` py
Example 1: Finding the even numbers.

## Traditional Loop

nums = [1, 4, 5, 6, 7, 8, 9]

even_nums = []
for num in nums:
    if num % 2 == 0:
        even_nums.append(num)


## One-liner
even_nums = list(filter(lambda x: x % 2 == 0, nums))
Example 2: Filter out all the numbers that are divisible by both 3 and 5.

numbers = [10, 15, 20, 30, 45, 50, 60]

## Traditional Function 
def div_by_3_5(nums):
  result = []
  for num in nums:
    if num % 3 == 0 and num % 5 == 0:
      result.append(num)


## One liner 
result = list(filter(lambda num: num % 3 == 0 and num % 5 == 0, numbers))

print(result)
```

#### Reduce

- `reduce(func, iterable)`
- The reduce() function in Python is a built-in function in the functools module.
- It applies a rolling computation to sequential pairs of elements(taking 2 elements at a time) in a list and returns a single output.

``` py
# Sum numbers using reduce

nums = [1, 2, 3, 4, 5]

## Traditional function 
def sum_nums(nums):
    result = 0
    for num in nums:
        result += num
    return result

print(sum_nums(nums))  
>>> Output: 15


## using reduce 
from functools import reduce

sum_nums = reduce(lambda x, y: x + y, nums)
print(sum_nums)  
>>> Output: 15


# Example 2: Find the product of all the even numbers in the list

my_list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

def product_of_even(my_list):
  product = 1
  for num in my_list:
    if num % 2 == 0:
        product *= num

## with Reduce and filter 
product = reduce(lambda x, y: x * y, filter(lambda x: x % 2 == 0, my_list))
```
