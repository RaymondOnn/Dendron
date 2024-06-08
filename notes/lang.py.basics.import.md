---
id: 82b9k345pkczm5ba4xlurcg
title: import
desc: ''
updated: 1713911990976
created: 1694226568563
---

<https://www.youtube.com/watch?v=QCSz0j8tGmI>
<https://www.geeksforgeeks.org/how-to-dynamically-load-modules-or-classes-in-python/>
https://www.youtube.com/watch?v=BMgk10UfoPU&ab_channel=Indently
https://www.youtube.com/watch?v=CraNpITZwRo


## Importing Modules

Python offers several ways to import modules:

- Regular Import: `import module_name`
  - With this approach, the module’s contents can be accessed using the module_name namespace.
- Import with Alias: `import module_name as alias`
  - Creating an alias provides a shorter, more convenient reference to the module.
- Import Specific Items: `from module_name import item_name`
  - This method directly imports specific items from the module into the current namespace.
- Import Everything: `from module_name import *`
  - While concise, this approach might lead to namespace pollution and reduced code readability.

## The `__init__.py`

- The `__init__.py` file is executed when the module is imported 
- Often used to initialize variables, set up resources, or define module-level attributes.
- Effectively transforms a directory into a Python package, allowing for more structured organization of code.

### Use Cases of `__init__.py`

#### Organizing Modules

- Import all of these modules in the `__init__.py` file.
- Allows the code can be imported directly via the main module, and you won't need to import code from separate modules anymore.

``` py
# math_pkg/__init__.py
from .arithmetic import add, subtract, multiply, divide
from .statistics import mean, median, mode

# Importing and Using
from math_pkg import add, mean

result = add(10, 5)
average = mean([2, 4, 6, 8])
```

#### Package-Level Utility Functions

- You can also define these functions directly in the `__init__.py` file for easy access.
- Allows for direct access the utility functions without navigating through submodules.

``` py
# string_utils/__init__.py
def reverse_string(input_str):
    return input_str[::-1]

def capitalize_words(input_str):
    return ' '.join([word.capitalize() for word in input_str.split()])

# Using the Utility Functions
from string_utils import reverse_string, capitalize_words

reversed = reverse_string("hello")
capitalized = capitalize_words("python is awesome")
```

#### Package-Level Configuration Class

- Here, the `__init__.py` file provides a convenient class for users to configure and manage settings related to your package.

``` py
# my_package/__init__.py
class Configuration:
    def __init__(self):
        self.api_key = None
        self.debug_mode = False

    def set_api_key(self, key):
        self.api_key = key

    def enable_debug(self):
        self.debug_mode = True

# Using the Configuration Class
from my_package import Configuration

config = Configuration()
config.set_api_key("your_api_key_here")
config.enable_debug()

print(config.api_key)  # Output: your_api_key_here
print(config.debug_mode)  # Output: True
```
