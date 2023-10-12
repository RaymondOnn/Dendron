---
id: 1ealoc83bpx663wnv0u0u2e
title: type_hints
desc: ''
updated: 1695093248312
created: 1693672520500
---

https://www.youtube.com/watch?v=QORvB-_mbZ0&ab_channel=TechWithTim

## Type Annotation: Why

- Help write shorter documentation by making the data type explicit
- Helps IDE with autocompletion and detecting errors
- Make coupling more explicit. You are more aware of the input types required for the coupling
- 

## Type Annotation: How

``` py
# Type annotation are just documentation
# No impact on the running of the code regardless if they are added

x: int = 1
x: str = 'tim'

```

### Adding type hints to functions

```py

# None if function does not return any value
def add_number(a:int, b:int) -> int:
    return a + b

```

## Static Code Analysis

- Will require the module `mypy`. To install, `pip install mypy`
- To run analysis, run command below in terminal
``` bash
# to run analysis, run command in terminal
mypy <file_name.py>
```

## Advanced Types

``` py
# List of List of Integers
from typing import List
x: List[List[int]] = [[1, 2, 3]. [4, 5, 6]]

# from python 3.9
x: list[list[int]] = [[1, 2, 3]. [4, 5, 6]]


# Dictionary with key (str) and value (str)
from typing import Dict
x:dict[str, str] = {"a": "b"}

# Set of strings
from tying import Set
x: Set[str] = {"a", "b"}

# Tuples
from typing import Tuple
# Will need to specify for each element
x: tuple[int, int, int, str] = {1, 2, 3, s}
```

### Special Types

```py
# CONSTANTS

from typing import Final

VERSIONl Final[int] = 10

# ---------------------------------------

# CUSTOM TYPE
from typing import List

Vector = List[float]   # storing a type in a 'variable'

def foo(v:Vector) -> Vector:   # param v of type Vector which is a list of floats
    print(v)

# ---------------------------------------

# OPTIONAL TYPE
from typing import Optional
# Optional indicates that the param is optional
def foo(output: Optional[bool]=False):
    pass

# ---------------------------------------

# ANY TYPE

from typing import Any

def foo(output: Any):
    pass    

# ---------------------------------------

# SEQUENCE TYPE
# Works regardless of data type i.e. list, dict, tuple) the sequence is stored in
from typing import Sequence

def foo(seq: Sequence[str]):
    pass

foo(("a", "b", "c"))
foo(["a", "b", "c"])

# ---------------------------------------

# CALLABLE TYPE
# For functions

from typing import Callable

def add(x: int, y: int) -> int:
    return x + y

# implies needing a function as param
# the input function requires two params of type int and returns output of type int
def foo(func: Callable[[int, int], int]) -> None:
    func()

foo(add(1, 2))

# ---------------------------------------

# the Union Operator (from python 3.10)
# Before 3.10, use typing.Union i.e. Union[int, float]
def foo(x: int | float) -> int | float
    ...
```

### Generic Type

``` py
from typing import TypeVar

# T acts as a placeholder and works for any type
T = TypeVar('T')

def get_item(lst: list[T], index:int) -> T:
    return lst[index]

    
```


ParamSpec
Concatenate
TypeAlias
Literal
TypeGuard