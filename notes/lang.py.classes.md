---
id: us7md8g2ovpyeepw2n71ymp
title: Classes
desc: ''
updated: 1692087863400
created: 1691590806329
---

link: https://blog.tobked.dev/enum-and-dataclass.html

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


## Enums

- Enum is useful when you need immutable name-value pairs enclosed in an iterable object.

<br>

``` py
from enum import Enum

class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3


>>> repr(Color.RED))
'<Color.RED: 1>'
>>> type(Color.RED)          
<enum 'Color'>

# access
>>> Color(1)        # __call__ method is invoked
<Color.RED: 1>
>>> Color(3)
<Color.BLUE: 3>
>>> Color['RED']
<Color.RED: 1>
>>> Color['GREEN']
<Color.GREEN: 2>

>>> member = Color.RED 
>>> member.name                     
'RED'
>>> member.value                    
1
```


## Dataclasses
- Dataclasses provide elegant syntax for creating mutable data holder objects. 

<br>

```py
from dataclasses import dataclass, asdict, astuple, replace


@dataclass
class Color:
    hue: int
    saturation: float
    lightness: float = 0.5



# __init__
>>> c = Color(33, 1.0)
>>> c
Color(hue=33, saturation=1.0, lightness=0.5)

>>> c.hue
33
>>> c.saturation
1.0
>>> c.lightness
0.5

>>> replace(c, hue=120)
Color(hue=120, saturation=1.0, lightness=0.5)
>>> asdict(c)
{'hue': 33, 'saturation': 1.0, 'lightness': 0.5}
>>> astuple(c)
(33, 1.0, 0.5)
```