---
id: tml04k6ixygmhpo27v2e285
title: Generators
desc: ''
updated: 1695137668855
created: 1695002900771
---

Yield as birectional communication??
Yield with itertools?



## What is a generator?

- Functions that uses the `yield` keyword
- When the interpreter hits the `yield` statement, it pauses and 'yield' a value
- Lazy evaluation: Processing only occurs when needed

``` py
def get_values():
    yield "hello"
    yield "world"
    yield 123
```

## Why generators?

- Solve the problems of iterators having only the first and last values e.g. range(1, 10)
- Uses less memory
- Faster performance

## How do we use generators?

### Converting a result into a generator

<table>
  <tr>
    <th>Before</th>
    <th>After</th>
  </tr>
  <tr>
    <td>

```py
def square_numbers(nums: list[int]):
    result = []
    for i in nums:
        result.append(i*i)
    return result

my_nums = square([1, 2, 3, 4, 5])
print(my_nums)
>>> [1, 4, 9, 16, 25]
```

</td>
<td>

``` py
def square_numbers(nums: list[int]):
    for i in nums:
        yield(i*i)

my_nums = square([1, 2, 3, 4, 5])
print(my_nums)
>>> <generator object square_numbers at ...>
```

Why did we get a generator object?

- Generators don't store the results in memory
- Instead, the generator object yield the results, one at a time, when called.
- By returning the generator object, it's waiting for us to get the next result

</td>
  </tr>
</table>

### Running the generator object

- To run the generator object:
  - we can use the `next()` keyword and yield the next value
  - This behaviour continues until there is nothing left to yield.
  - In that case, we get an StopIteration error

``` py
def square_numbers(nums: list[int]):
    for i in nums:
        yield(i*i)

my_nums = square([1, 2, 3, 4, 5])

print(next(my_nums))
print(next(my_nums))
print(next(my_nums))
print(next(my_nums))
print(next(my_nums))
print(next(my_nums))
>>> 1
>>> 4
>>> 9
>>> 16
>>> 25
>>> StopIteration

my_nums = square([1, 2, 3, 4, 5])

print(next(my_nums))
print(next(my_nums))
print(list(my_nums)) # list contain results that have not 'yield'-ed yet
>>> 1
>>> 4
>>> [9, 16, 25]
```

#### Running the generator object: A better way

A better way to run the generator object is to use a `for` loop

``` py
def square_numbers(nums: list[int]):
    for i in nums:
        yield(i*i)

my_nums = square([1, 2, 3, 4, 5])

for num in my_nums:
    print(num)
>>> 1
>>> 4
>>> 9
>>> 16
>>> 25
```

### Generator Comprehension

Rewriting the generation function into a generator comprehension

``` py
my_nums = (x*x for x in [1, 2, 3, 4, 5])

print(my_nums)
for num in my_nums:
    print(num)
>>> <generator object square_numbers at ...>    
>>> 1
>>> 4
>>> 9
>>> 16
>>> 25
```

### [Joining Generators](https://www.markhneedham.com/blog/2015/05/24/python-joining-multiple-generatorsiterators/)

```py
import itertools

gens = [(n*2 for n in range(0, 3)), (n*2 for n in range(4,7))]

output = itertools.chain()
for gen in gens:
    output = itertools.chain(output, gen)
    
for item in output:
    print(item)
```
            

### Use Cases

#### Reading from files

```py
class MyDataPoint(NamedTuple):
    x: float
    y: float
    z: float

# process file line by line
# Size of file now doesnt affect memory usage since memory is needed one line at a time
def my_data_reader(file):
    for row in file:
        cols = row.rstrip().splot(",")
        cols = [float(c) for c in cols]
        yield MyDataPoint._make(cols)

def example_reader():
    with open('data.txt') as f:
        for row in my_data_reader(f):
            print(row)
```

#### Generator Pipelines

``` py
def example_pipeline():
    with open("numd.txt") as f:
        nums = (row.partition("#")[0].rstrip() for row in file) # remove comments
        nums = (row for row in nums if row) # remove empty lines
        nums = (float(row) for row in nums) # convert to float
        nums = (x for x in nums if math.isfinite(x)) # remove infinite & nan values
        nums = (max(0., x) for x in nums) # remove negative numbers
        s = sum(nums) # aggregate values. 
        print(f"the sum is {s}")
```

### Advanced usage: `send`, `throw`, `close`

#### `yield` as an expression

``` py
def worker(f):
    # worker has a collection of task, that start empty
    tasks = collections.deque()
    value = None
    while True:
        batch = yield value
        value = None

        # if there are new tasks
        if batch is not None:
            tasks.extend(batch)
        else:
            # complete tasks
            if tasks:
                args = tasks.popleft()
                value = f(*args)

def example_worker():
    w = worker(str)
    w.send(None)
    w.send([(1,),(2,), (3,)])
    print(next(w))
    print(next(w))
    print(next(w))
    
    # w.throw(ValueError)
    w.close()
```

### `yield from`

- Allows one generator to yield from another generator

**Basic Usage**

```py
def func():
    yield from (x*x for x in range(5))

# Same functionality as below
def func():
    for sq in (x*x for x in range(5))
        yield sq
```

---
https://gist.github.com/kolypto/3240037e46bce47d4374331decc298f1
https://docs.python.org/3/library/typing.html#typing.Generator
https://stackoverflow.com/questions/9708902/in-practice-what-are-the-main-uses-for-the-yield-from-syntax-in-python-3-3
https://medium.com/swlh/what-is-yield-or-generator-in-python-4484a11362d0

## Simple generator: yield values one by one

``` py
# This function pauses to return a value to the outer scope, then proceeds

DICTIONARY = {
    'a': 'apple',
    'b': 'banana',
    'c': 'cat',
    'd': 'dog',
    'e': 'egoseismic',
    'f': 'facepalm',
    'g': 'gringo',
    'h': 'hilarious',
    'i': 'iPhone',
    'j': 'John Lennon',
    'k': 'KDE',
    'l': 'lemonsaurus',
    'm': 'Mickey Mouse',
    'n': 'Netherrealm',
    'o': 'of course',
    'p': 'pokerface',
}

def alphabet():
    for letter, word in DICTIONARY.items():
        yield word  # yield values one by one

# High-level API: just iterate it

for word in alphabet():
    print(word)

>>> apple
>>> banana
>>>


# Low-level usage: use next() to obtain another value

g = alphabet()
print(g)  # -> <generator object alphabet at 0x7f425c7bbe40>
word = next(g)  # 'apple'
print(word)  # -> 'apple'

# WHen it reaches the end, a StopIteration error is raised
# This generator `g` will raise a StopIteration immediately because it's been exhausted in the previous example
# It has nothing else to yield
try:
    while True:
        word = next(g)  # raises: StopIteration
        print(word)
except StopIteration:
    print('-- done')

# Generators can also receive values: they support bi-directional communication
# The `yield` keyword can also *receive* values: a generator becomes a "listener" for incoming values

# In this example, `alphabet()` gets a value

def alphabet():
    """ Example generator with bi-directional data flow
    Input: letters
    Output: words
    """
    word = None
    while True:
        # Communication point:
        # Input: a letter
        # Output: a word
        letter = yield word
        word = DICTIONARY[letter]  # convert it into a word

    # You can also return a value.
    # We'll check this example out later
    return word  # Speak your final word

g = alphabet()
next(g)  # Initialize the generator. It returns `None` (the initial word)

print(g.send('e'))  # egoseismic
print(g.send('f'))  # facepalm
print(g.send('h'))  # hilarious
g.close()  # we're done with it. Returns `None`, always

#  It is possible to raise an error "into" the generator and catch it there

def alphabet():
    word = None
    while True:
        # Communication point
        try:
            letter = yield word
        # If we were given an exception, return a default word
        except KeyError:
            word = '(default)'
        else:
            word = DICTIONARY[letter]  # convert it into a word

g = alphabet()
next(g)  # Initialize the generator. It returns `None` (the initial word)

print(g.throw(KeyError()))  # (default)
print(g.send('i'))  # iPhone
g.close()

# NOTE that

# When a generator throws an exception, it exits. 
# You can't continue consuming the items it generates
#  A generator that uses `yield` to request data from the outside

# This use case, however, is a little inside-out. Makes very little sense. But you can :)

def gimme_words():
    # Start building a sentence
    sentence = []
    for letter in 'abcdef':
        # Give every letter to some external caller and expect them to give us a word
        word = yield letter
        # Keep building the sentence
        sentence.append(word)

    # Return the sentence!
    return ' '.join(sentence)

# Initialize the generator

g = gimme_words()

# Keep going while we can

try:
    word = None
    while True:
        # Send the initial `None` to the generator. We just have to.
        letter = g.send(word)
        # Convert the letter that the generator gives us into a word and give it back to the generator
        word = DICTIONARY[letter]

# When done, print the resulting value

except StopIteration as e:
    print(e.value)
finally:
    g.close()

```