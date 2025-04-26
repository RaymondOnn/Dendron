---
id: trkqdbu0wgn2fzl1hf5avpm
title: monads
desc: ''
updated: 1730533959670
created: 1730527395358
---

<https://dev.to/hamzzak/mastering-monad-design-patterns-simplify-your-python-code-and-boost-efficiency-kal>

### Monad Design Pattern

- Monad is a functional programming design pattern that enables you to combine several calculations or functions into a single expression while also managing error circumstances and side effects.
- Every function in the chain should, in theory, return a new monad that may be used as input by the function after it.

### Types of Monad

- Here are a few examples:
  - **Maybe Monad**:
    - Represents a computation that **may or may not return a value**.
    - This is useful for handling error conditions or optional values.
  - **State Monad**:
    - Represents a computation that **maintains an internal state** that is passed from one function to the next.
    - This can be useful for modeling simulations or other computations that require tracking changes over time.
  - **Reader Monad**:
    - Represents a computation that has **access to a shared environment or configuration data.**
    - This can be useful for parameterizing computations and making them more reusable.
  - **Writer Monad**:
    - Represents a computation that **generates output or side effects**.
    - This can be useful for logging, debugging, or other kinds of diagnostics.
  - **IO Monad**:
    - Represents a computation that**performs input/output operations** or other kinds of side effects.
    - This can be useful for interacting with external systems, such as databases or web services.

- Each Monad has its own set of operations that define how computations can be chained together and how values can be transformed or combined.
- However, all Monads share the property of being composable and modular, which makes them a powerful tool for building complex computations in a functional style.

### Maybe Monad

- In Python, the Monad design pattern can be implemented using classes and operator overloading.
- Here's an example of the Maybe Monad, which represents a computation that may or may not return a value:
  - The Maybe class represents a computation that may or may not return a value.
  - `bind`: takes a function as input and returns a new Maybe instance that represents the result of applying the function to the original value, if it exists.
  - The `|` operator can be used to combine two Maybe instances, returning the first one that contains a value.
  - The `add_one` and `double` functions represent computations.
    - These functions can be chained together using the `bind method` to create more complex computations that can handle error conditions and side effects.
- While not a commonly used pattern in Python, the pattern can be useful in situations where you need to chain computations together in a more modular and reusable way.

``` py
class Maybe:
    def **init**(self, value):
        self._value = value

    def bind(self, func):
        if self._value is None:
            return Maybe(None)
        else:
            return Maybe(func(self._value))

    def orElse(self, default):
        if self._value is None:
            return Maybe(default)
        else:
            return self

    def unwrap(self):
        return self._value

    def __or__(self, other):
        return Maybe(self._value or other._value)

    def __str__(self):
        if self._value is None:
            return 'Nothing'
        else:
            return 'Just {}'.format(self._value)

    def __repr__(self):
        return str(self)

    def __eq__(self, other):
        if isinstance(other, Maybe):
            return self._value == other._value
        else:
            return False

    def __ne__(self, other):
        return not (self == other)

    def __bool__(self):
        return self._value is not None

def add_one(x):
    return x + 1

def double(x):
    return x * 2

result = Maybe(3).bind(add_one).bind(double)
print(result)  # Just 8

result = Maybe(None).bind(add_one).bind(double)
print(result)  # Nothing

result = Maybe(None).bind(add_one).bind(double).orElse(10)
print(result)  # Just 10

result = Maybe(None) | Maybe(1)
print(result) # Just 1
```

### State Monad

- The state monad allows you to encapsulate a stateful computation as a pure function that takes an initial state and returns a new state and a result.
- The state is typically represented as a data structure, and the function performs computations that update the state as needed.
- In Python, you can implement the state monad using classes and closures.
- The basic idea is to define a class that represents a stateful computation, and use closures to create new stateful computations that depend on the current state.
- The `call` method of the class is used to define the actual computation, and it returns a new instance of the class with an updated state and a result.
- Here's a simple example of using the state monad in Python to perform a stateful computation that counts the number of times a function is called:

 The call method is the actual computation, which returns a tuple containing the result and a new instance of the State class with an updated state.
We then create an instance of the State class called counter that represents the stateful computation that counts the number of times it is called. We call the computation multiple times using a loop, and print the current count and result after each call.

``` py
class State:
    # state represented with tuple: the count and the result. 
    def __init__(self, state):
        self.state = state

    def __call__(self, value):
        return (self.state[1], State((self.state[0] + 1, value)))

# create a stateful computation that counts the number of times it is called

counter = State((0, 0))

# call the computation multiple times and print the current count

for i in range(5):
    result, counter = counter(i)
    print(f"Computation result: {result}, count: {counter.state[0]}")

# Computation result: 0, count: 1
# Computation result: 0, count: 2
# Computation result: 1, count: 3
# Computation result: 2, count: 4
# Computation result: 3, count: 5
```

- The benefits of using the state monad in Python include the ability to write pure functions that encapsulate stateful computations, which can improve code clarity and maintainability.
- By separating the stateful computation from the rest of the code, you can write more modular and testable code that is easier to reason about.
- The use of closures make it easier to write stateful computations that depend on the current state, and can simplify code that would otherwise be more complex to write and maintain.

### Reader Monad

- The Reader monad allows you to pass around an immutable environment to a function, so that the function can access values from the environment without having to explicitly pass them as arguments.
- In the Reader monad, the environment is modeled as a function that takes a single argument and returns a value. 
- The function that uses the environment is then wrapped in a monadic context, so that it can be composed with other monadic functions.
- Here's an example that demonstrates the basic usage of the Reader monad in Python:

``` py
from typing import Any, Callable, TypeVar

T = TypeVar('T')
def reader(f: Callable[[Any], T]) -> Callable[[Any], T]:
    def wrapped(*args):
        return f(*args)
    return wrapped

def greet(name: str) -> str:
    return f"Hello, {name}!"

greet_reader = reader(greet)

# call greet_reader with the name argument

result = greet_reader("Alpha")

print(result) # output: "Hello, Alpha!"
```

In this example, the reader function is a helper function that returns a wrapped function that takes a single argument. The wrapped function calls the original function with the argument.
Here, greet function takes a single argument, name, and returns a string. The greet_reader function is created by calling the reader function with the greet function as an argument. The greet_reader function takes a single argument, name, and returns the result of calling greet with the name argument.
Using Reader monad for Configuration:
``` py
from typing import Dict, Callable, TypeVar

T = TypeVar('T')
def reader(f: Callable[..., T]) -> Callable[..., T]:
    def wrapped(*args, **kwargs):
        config = kwargs.get('config')
        return f(config,*args)
    return wrapped

@reader
def greet(config: Dict[str, str]) -> str:
    return f"Hi, {config['name']}"

result = greet(config={'name':'Beta'})
print(result)
```
In this example, the reader function takes a function as an argument and returns a wrapped function. The wrapped function takes an additional keyword argument, config, which is used to pass a configuration dictionary to the function.
By decorating a function with the reader decorator, you are creating a new function that expects a config keyword argument and passes it to the decorated function. This allows you to separate the configuration data from the rest of your function's logic.
In the example code, the greet function is decorated with the reader decorator. This means that when you call the greet function using greet(config={"name": "Beta"}), the config dictionary is passed to the decorated function, and the resulting string is returned.
The greet function itself takes a config dictionary as its argument and returns a string greeting the person whose name is specified in the config dictionary. The config argument is passed to the greet function via the wrapped function created by the reader decorator.
These are just some simple examples of using the Reader monad in Python. The concept can be applied to a wide range of scenarios where there are dependencies between functions.
Overall, the Reader monad can be a powerful tool for building functional programs in Python, especially when working with complex and nested data structures.

### Writer Monad
The Writer Monad allows us to perform computations while accumulating a log or other auxiliary information. It is similar to the Reader monad in that it separates some aspect of your program's behavior (in this case, logging or accumulation) from the rest of your application logic.
In Python, you can implement the Writer monad using a combination of a tuple and a function that takes a value and a log, and returns a new value and log. This function is usually called the "writer function".
``` py
from typing import Tuple

def writer(value, log):
    return (value, log)

def add(x, y):
    result = x + y
    log = f"Adding {x} and {y} to get {result}.\n"
    return writer(result, log)

def multiply(x, y):
    result = x * y
    log = f"Multiplying {x} and {y} to get {result}.\n"
    return writer(result, log)

# Chain together add and multiply using the Writer monad

add_result, add_log = add(2, 3)
mul_result, mul_log = multiply(add_result, 4)
result = mul_result
log = add_log + mul_log
print(f"Result: {result}")
print(f"Log: {log}")

# result: 20
# log: Adding 2 and 3 to get 5
# Multiplying 5 and 4 to get 20
```
In this example, the writer function takes a value and a log as its arguments and returns a tuple containing the value and log. The add and multiply functions perform addition and multiplication, respectively, and also generate log messages using formatted strings.
This demonstrates how the Writer monad can be used to accumulate log messages as your program runs, making it easier to debug and understand the behavior of your code.
Another example of the Writer monad might involve accumulating a list of values as your program runs, or maintaining a running total of some quantity. The basic idea is the same: use a tuple and a writer function to accumulate values or logs, and chain together functions using the partial function to combine them into a larger computation.

### IO Monad
The IO monad is a way of dealing with input and output in a purely functional way. In Python, the IO monad can be implemented using a class with a single method call that takes no arguments and returns the result of the IO operation.
Here's an example of how you might use the IO monad in Python to read a file and print its contents:

``` py
class IO:
    def **init**(self, effect):
        self.effect = effect

    def __call__(self):
        return self.effect()

def read_file(filename):
    def read_file_effect():
        with open(filename, 'r') as f:
            return f.read()

    return IO(read_file_effect)

def print_contents(contents):
    def print_effect():
        print(contents)

    return IO(print_effect)

# chain the IO operations manually

contents = read_file('example.txt')()
print_contents(contents)()
```
In this example, we call read_file() to create an IO object that reads the contents of a file. We then call the call() method of this object to execute the IO operation and retrieve the contents of the file. We store the contents in the contents variable and pass it as an argument to print_contents() which creates another IO object that prints the contents to the console. Finally, we call the call() method of the print_contents() object to execute the IO operation and print the contents of the file to the console.
