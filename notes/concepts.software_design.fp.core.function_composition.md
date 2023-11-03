---
id: km0r4t9yedlmqtc9ur6hhmz
title: function_composition
desc: ''
updated: 1697312133619
created: 1697311924860
---


## Function composition

Function composition occurs when you create a new function by combining multiple smaller functions. This is an effective way of abstracting complex behaviours from several functions into one simple function that acts as an API.

Imagine flying from one country to another. As a flight passenger, all you care about is just getting to your destination in a safe and relaxed manner - many complex activities are occurring during your entire flight to make that possible that are abstracted (hidden) from you.

### Examples

#### Without function composition🚫

``` py
def strip_whitespace(data):
    return data.strip()

def use_lowercase(data):
    return data.lower()

def remove_dollar_sign(data):
    return data.replace('$', '')

dummy_data              =   "  $100.00  "
no_space_data           =   strip_whitespace(dummy_data)
lowercase_data          =   use_lowercase(no_space_data)
no_dollar_data          =   remove_dollar_sign(lowercase_data)

print(no_dollar_data)
>>> 100.00
```

This example doesn’t comply with function composition because the functions are applied sequentially without forming a new function in the process.

#### With function composition✔️

Here’s an example:

``` py
# You can implement composition by creating helper functions by chaining functions together.
from functools import reduce

# Create data cleaning functions
def strip_whitespace(data):
    return data.strip()

def use_lowercase(data):
    return data.lower()

def remove_dollar_sign(data):
    return data.replace('$', '')

# Create helper function
def compose_data_cleaning_functions(*functions):
    def compose(x):
        return reduce(lambda v, f: f(v), functions, x)
    return compose

# Use helper function to combine cleaning functions into one new function
transform_data           =   compose_data_cleaning_functions(strip_whitespace, use_lowercase, remove_dollar_sign)
dummy_data               =   "  $100.00  "

cleaned_data            =   transform_data(dummy_data)
print(cleaned_data)
>>> 100.00
```

We’ve now been able to implement composition by creating a helper function called compose_data_cleaning_functions to allow us to create an API called transform_data, which performs the complex hidden data cleaning jobs behind the scenes.
