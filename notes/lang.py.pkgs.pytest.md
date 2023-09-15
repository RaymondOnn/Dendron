---
id: ov21izw706cnwbg3560776m
title: Pytest
desc: ''
updated: 1693980691448
created: 1693978028033
---
https://www.youtube.com/watch?v=ofPHJrAOaTE&ab_channel=PyConDE
https://www.youtube.com/watch?v=qQ6b7OwT124&ab_channel=TalkPython

## What is a test?

A test is code tht runs to check the validity of other code

## Why Test?

- Prove that the code wirks like I think it should
- Give examples to others of how code is used
- Get a safety net for future changes

## What is pytest?

- Basis for a rick ecosystem of testing plugin and extensions
- looks for files and functions starting with 'test_'

``` py
# test_calculator,py

def test_add():
    assert True
```

## Arrange, Act, Assert

``` py
# calculator.py

class Calculator:
    def add(self, a, b):
        return a + b


# test_calculator.py

from calculator import Calculator

def test_add():
    # Arrange: Set up the inputs
    calculator = Calculator()

    # Act: Call the function to be tested
    result = calculator.add(2, 3)

    # Assert: Check output is as expected
    assert result == 5

```
## Running tests

## Happy Path


## Exceptions

```py
# calculator.py

class CalculatorError(Exception):
    """An exception class for Calculator"""

class Calculator:
    def add(self, a, b):
        try:
            return a + b
        except TypeError:
            raise CalculatorError()

# test_calculator.py

import pytest
from calculator import Calculator, CalculatorError

def test_add_weird_stuff()
    calculator = Calculator()

    with pytest.raises(CalculatorError):
        result - calculator.add("two", 3)
```
