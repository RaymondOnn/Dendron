---
id: eiznxhtsxrj4xohmdkt7v8e
title: dataclasses
desc: ''
updated: 1698187061241
created: 1697519042106
---


## Dataclasses

- Data Classes reduce the amount of code you need to write by automatically generating useful special methods such as **init**, **repr**, **eq**, and **hash**.
- Dataclasses provide elegant syntax for creating mutable data holder objects.
- To define a dataclass, you can use the @dataclass decorator and define the class attributes

### Why dataclasses

- By building relationships using Data Classes, you are adding to the shared vocabulary in your codebase. You can easily impart domain-specific knowledge to future users and maintainers of your code.
- Data classes force you to explicitly assign types to your fields, so there’s less chance of type confusion among maintainers.
- Data classes are easy to read and understand, and the typechecker naturally knows how to handle them.
- Note that a Data Class is only really appropriate when the members within the data class are independent of one another. For more complex relationships, or when you need further control over behaviour, you should use classes instead

```py
from dataclasses import dataclass, asdict, astuple, replace


@dataclass
class Color:
    hue: int
    saturation: float
    lightness: float = 0.5

# __init__
c = Color(33, 1.0)
c
>>>Color(hue=33, saturation=1.0, lightness=0.5)

 c.hue
>>> 33
c.saturation
>>> 1.0
c.lightness
>>> 0.5

replace(c, hue=120)
>>> Color(hue=120, saturation=1.0, lightness=0.5)
asdict(c)
>>> {'hue': 33, 'saturation': 1.0, 'lightness': 0.5}
astuple(c)
>>> (33, 1.0, 0.5)
```