---
id: us7md8g2ovpyeepw2n71ymp
title: classes
desc: ''
updated: 1697907892311
created: 1691590806329
---

link: <https://blog.tobked.dev/enum-and-dataclass.html>

Enum
Metaclasses
Dataclasses
ABC
Protocol

## Functions vs Classes

- Functions are **action-focused** and organised based on data flow
- Classes are **state-focused**; they are variables grouped into objects, grouped into other objects and methods modify the state
- The rule can be formulated as follows: if you have state that needs to be retained for longer than one procedure call (and that state can be meaningfully encapsulated), then use a class. If the state is only needed for one procedure call, use a procedure .

## Speed Up Class Performance

- If there's no need to change the attributes dynamically, using `__slots__` can help speed up performance

``` py
# Using slots
class Person:
    __slots__ = 'name', 'address', 'email'

    def __init__(self, name:str, address:str, email:str):
        ...

# Alternative version using dataclasses
from dataclassess import dataclass

@dataclass(slots=True)    # defaults to false
class Person:
    name: str
    address: str
    email: str
```


