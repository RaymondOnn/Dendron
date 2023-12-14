---
id: ov21izw706cnwbg3560776m
title: Pytest
desc: ''
updated: 1701561257573
created: 1693978028033
---
Mocks: https://pytest-with-eric.com/pytest-best-practices/pytest-mock-assert-called/

## [Pytest](https://medium.com/@pragprog/table-of-contents-86377afafc57)

### What is Pytest

- Testing framework for Python
- pytest is a software test framework,
- a command-line tool that automatically finds and runs the tests and reports the results.
- It can be extended via plugins.
- It can be used to test Python distributions
- Easy integration with other tools like continuous integration and web automation.

### Why Pytest

- Simple tests are simple to write in pytest.
- Complex tests are still simple to write.
- Tests are easy to read.
- You can get started in seconds.
- You use assert to fail a test, not things like self.assertEqual() or self.assertLessThan(). Just assert.
- You can use pytest to run tests written for unittest or nose.

### Installation

``` sh
conda install -c anaconda pytest
pip install pytest
```

### The Testing Environment

#### Directory Structure

- All tests must be written in a tests/ directory

``` md
 project_directory
 ├── src
 |   └── my_code.py
 └── tests
     └── test_my_code.py

```

#### Naming Conventions

- By default, to indicate a file as a test file, start the filename with 'test_'
- To indicate a function as a test function, start the function name with test

    ``` py
    #test_example.py

    def test_add():
        assert 1 + 1 == 2
    ```

#### Configurations

- You can customize various options like adding command line arguments, setting the python path, and more via  config files i.e. pytest.ini, pyproject.toml, or tox.ini.

``` raw
# pytest.ini file
minversion = 6.0    # specify minimum pytest version
addopts = -ra -q    # add cli options
testpaths = test    # redirect pytest to tests folder
```

### Test Functions

- A test function is a Python function that verifies a specific aspect of your code’s behavior.
- Each test function should be self-contained, focusing on a single test case.

#### Structure of a Test Function

- See [Arrange, Act, Assert]
- In pytest, assertions are made within the function.
  - These assertions are used to test the function's behavior.
  - Pytest provides a wide range of powerful assertion functions.

#### A List of Some Asserts Functions

- Here are some of the main assertions provided by Pytest:
  - assert: Make simple assertions by checking if a given condition is true
  - assertEqual: Compare two values for equality. It checks if the expected value is equal to the actual value
  - assertTrue and assertFalse: Verify if a given condition is true or false.
  - assertRaises: Check if a specific exception is raised when executing a code.
  - assertIn and assertNotIn: Verify if a value is present or absent in a given collection or sequence
  - assertAlmostEqual: Compare floating-point values to a specified degree of precision.
  - assertDictEqual: Check if two dictionaries have the same set of key-value pairs.
  - assertRaisesRegex: Verify if a specific exception is raised and the exception message matches a regular expression pattern.

``` py
def test_function_with_scenario_one():
    print("Testing function with scenario one")
    assert 1 + 1 == 2, f"Check addition value {1 + 1} does not match {2}"
```

### Writing Tests

- Steps for writing test functions
  1. Create a new script `test_<mytest>.py` for your tests. Import Pytest into the script.
  2. Write the Test Functions
     - Test Fixtures: Fixtures are functions that provide reusable and preconfigured resources or test data to your test functions.

#### Example

Suppose you have a function add in a file named maths.py:

``` PY

# maths.py
def add(a, b):
    return a + b

# -----------------------------------------------------

# test_maths.py
from maths import add

def test_add():
    assert add(2, 3) == 5
```
