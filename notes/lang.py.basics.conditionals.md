---
id: v77hhgkg6zdp3h3ferrpy5g
title: conditionals
desc: ''
updated: 1697907786622
created: 1697272386981
---

- numpy.where()

### Simplifying if-else statements

#### Unnecessary else

If the else block is not necessary, then if a certain condition is met, just return something immediately.

``` py
# before
def is_even(number):
    if number % 2 == 0:
        return True
    else:
        return False

# after
def is_even(number):
    if number % 2 == 0:
        return True
    return False
```

#### Value Assignments

Assigning a default value can simplify your code a lot.

``` py
# before
if condition:
    number = 1
else:
    number = 2

# after
number = 2
if condition:
    number = 1

# -----------------------------------------------------------------

def classify_temperature(temperature):
    message = ''
    if temperature >= 26:
        message = "Hot"
    elif temperature >= 15:
        message = "Warm"
    else:
        message = "Cold"
    return message

def classify_temperature(temperature):
    if temperature >= 26:
        return "Hot"
    if temperature >= 15:
        return "Warm"
    return "Cold"
```

#### Guard Clauses

- By returning or raising an exception as soon as an early exit condition is met, it removes the need for else statements
- It makes no sense to let a function continue if it is provided with invalid inputs.
  
``` py
# Using if-else statements
def validate_input(input_list):
    if isinstance(input_list, list):
        if input_list:
            if all(isinstance(item, float) for item in input_list):
                return True
            else:
                raise ValueError("All items in the list must be floats.")
        else:
            raise ValueError("Input cannot be an empty list.")
    else:
        raise TypeError("Input must be a list.")
        raise ValueError("All items in the list must be floats.")

# Using guard clauses
def validate_input(input_list):
    if not isinstance(input_list, list):
        raise TypeError("Input must be a list.")
    if not input_list:
        raise ValueError("Input cannot be an empty list.")
    if not all(isinstance(item, float) for item in input_list):
    return True
```

#### Dictionary

- With a dictionary, we can map each input value to its corresponding action or value.
- Adding new cases only require modifying the dictionary
- Dictionary lookups are usually faster than iterating through a series of if-else conditions.

``` py
# lots of if-elif
def determine_favorite_fruit(color):
    if color == "red":
        return "Strawberry"
    elif color == "yellow":
        return "Banana"
    elif color == "green":
        return "Honeydew"
    return "unknown"


# Dictionary is used
def determine_favorite_fruit_with_dict(color):
    color_to_fruit = {
        "red": "Strawberry",
        "yellow": "Banana",
        "green": "Honeydew",
    }
    return color_to_fruit.get(color, "unknown")
```

#### Match

- Starting from Python 3.10, we can use match to perform pattern matching.
- In comparison to the if-elif method, match makes the code more readable.

``` py
def use_if_else(value):
    if value == 1:
        print("Value is 1")
    elif value == 2:
        print("Value is 2")
    elif value == 3:
        print("Value is 3")
    else:
        print("Value is something else")
        
        
def use_match(value):
    match value:
        case 1:
            print("Value is 1")
        case 2:
            print("Value is 2")
        case 3:
            print("Value is 3")
        case _:
            print("Value is something else")
```

#### Strategy Pattern

The [[concepts.software_design.oop.design_patterns.strategy]] pattern is a design pattern in software engineering that allows the behavior of a class to be selected at runtime.

``` py
from abc import ABC, abstractmethod


# Define the abstract Strategy class
class PaymentStrategy(ABC):
    @abstractmethod
    def pay(self, amount):
        pass

    
# Define the concrete Strategy classes
class CreditCardPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"Paying {amount} using credit card.")


class PayPalPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"Paying {amount} using PayPal.")

        
class StripePayment(PaymentStrategy):
    def pay(self, amount):
        print(f"Paying {amount} using Stripe.")
        
        
# Define the Context class that will use the Strategy pattern
class PaymentContext:
    def __init__(self, payment_strategy: PaymentStrategy):
        self.payment_strategy = payment_strategy

    def set_payment_strategy(self, payment_strategy: PaymentStrategy):
        self.payment_strategy = payment_strategy

    def pay(self, amount):
        self.payment_strategy.pay(amount)


# Example usage
payment_context = PaymentContext(CreditCardPayment())
payment_context.pay(100)  # Output: Paying 100 using credit card.

payment_context.set_payment_strategy(PayPalPayment())
payment_context.pay(50)  # Output: Paying 50 using PayPal.

payment_context.set_payment_strategy(StripePayment())
payment_context.pay(150)  # Output: Paying 150 using Stripe.
```

### Dictionary Dispatch

- The dictionary dispatch pattern allows us to run different functions based on the value of a variable instead of using a conditional statement for each value.
- With dictionary dispatch, we can replace any block of conditionals with a simple lookup into Python’s dict.
- However, if you require a more robust solution, you could use the [[concepts.software_design.oop.design_patterns.visitor]] pattern instead

``` py
# Using Lambda Functions ------------------------------
functions = {
    "add": lambda x, y: x + y,
    "mul": lambda x, y: x * y
}

print(functions["add"](5, 3))
>>> 8
print(functions["mul"](5, 3))
>>> 15

# Using Proper Functions ------------------------------

def add(x, y):
    return x + y

def mul(x, y):
    return x * y

functions = {
    "add": add,
    "mul": mul,
}

print(functions["add"](5, 3))
# 8
print(functions["mul"](5, 3))
# 15
```

#### Default Result

In case you want to use this pattern to emulate match/ case statements, consider using the default value when the dictionary key is not present.

``` p6
from collections import defaultdict

cases = defaultdict(lambda *args: lambda *a: "Invalid option", {
    "add": add,
    "mul": mul,
})

print(cases["add"](5, 3))
# 8
print(cases["_"](5, 3))
# Invalid option
```

#### Passing Arguments

###### Manipulating arguments prior to passing them to a function

e.g. look up a specific key in the payload

``` py
def handle_event(e):
    print(f"Handling event in 'handler_event' with {e}")
    return e

def handle_other_event(e):
    print(f"Handling event in 'handle_other_event' with {e}")
    return e

# With lambda:
functions = {
    "event1": lambda arg: handle_event(arg["some-key"]),
    "event2": lambda arg: handle_other_event(arg["some-other-key"]),
}

event = {
    "some-key": "value",
    "some-other-key": "different value",
}

print(functions["event1"](event))
>>> Handling event in 'handler_event' with value
>>> value
print(functions["event2"](event))
>>> Handling event in 'handle_other_event' with different value
>>> different value
```

###### Freezing certain arguments

``` py
event = {
    "some-key": "value",
    "some-other-key": "different value",
}

functions = {
    "event1": partial(handle_event, event["some-key"]),
    "event2": partial(handle_other_event, event["some-other-key"]),
}

print(functions["event1"]())
>>> Handling event in 'handler_event' with value
>>> value
print(functions["event2"]())
>>> Handling event in 'handle_other_event' with different value
>>> different value
```

#### An example

``` py
# parse_args.py
import argparse

functions = {
    "add": add,
    "mul": mul,
}

parser = argparse.ArgumentParser()

parser.add_argument(
    "operation",
    choices=["add", "mul"],
    help="operation to perform (add, mul)",
)
parser.add_argument(
    "x",
    type=int,
    help="first number",
)
parser.add_argument(
    "y",
    type=int,
    help="second number",
)

args = parser.parse_args()
answer = functions.get(args.operation,)(args.x, args.y)

print(answer)
```
