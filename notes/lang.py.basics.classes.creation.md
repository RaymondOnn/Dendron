---
id: pafqjuo8ixi3clzo3vcrjfa
title: creation
desc: ''
updated: 1729315753428
created: 1692225394069
---

## Creating Classes

### Tips for writing a better `__init__` method

#### Treat almost every class like a dataclass

- Think of an object as nothing more than a collection of related data.
- The class defines the names of the data fields and their types, and optionally implements methods to act on that data.
- The **init** method should do nothing more than assign this data

#### Move your object construction logic into factory methods or builders

- Easier to understand and reason about state. It is immediately clear what instance attributes are defined on an object after instantiation.
- Easier to test. Our construction functions are pure functions that can be called in isolation and do not rely on already existing state in the object.
- Easier to extend. We can easily implement additional factory methods to create a Configuration object in alternative ways, e.g. from a dictionary.
- Easier to be consistent. It’s easy to follow this approach in most of your classes than to constantly reinvent complex custom initialization logic.
- Builders are an alternative for factories when you need high levels of customization in the creation of your objects.

#### Adopt static type checking with mypy

- Static type analysis to resolve the issue of dynamic typing where variables can change type at any time

#### Improve encapsulation

- Make all instance variables private, meaning they can only be accessed by methods of the object itself.
- By convention any attribute that starts with \_ is assumed to be private.
- You can make instance variables almost truly private by prefacing them with \_\_.

#### Opt for immutability whenever possible

- Static state is much easier to reason about than mutating state.
- Enforcing immutability can be achieved in a number of ways.
- Use private instance variables and only expose them with a getter method, this contributes to immutability.
- Use immutable data structures like tuples over lists. If you can’t choose between dataclass or NamedTuple, NamedTuple should be preferred because its fields are immutable.

```py
from __future__ import annotations


def Configuration:
    def __init__(self, attr1: int, attr2: int) -> None:
        self._attr1 = attr1
        self._attr2 = attr2

    @property
    def attr1(self) -> int:
        return self._attr1

    @property
    def attr2(self) -> int:
        return self._attr2

    @classmethod
    def from_file(cls, filepath: str) -> Configuration:
        parsed_data = cls._parse_config_file(filepath)
        computed_data = cls._precompute_stuff(parsed_data)
        return cls(
            attr1=parsed_data,
            attr2=computed_data,
        )

    @classmethod
    def _parse_config_file(cls, filepath: str) -> int:
        # parse the file in filepath and return the data
        ...

    @classmethod
    def _precompute_stuff(cls, data: int) -> int:
        # use data parsed from a config file to calculate new data
        ...
```

### [Behind the scenes](https://www.youtube.com/watch?v=-js0K7Q878c&t=331s&ab_channel=PyCascades)

- When the keyword `class` is encountered
  - the body (statements and functions) of the class is isolated
  - the namespace dictionary of the class is created (but not populated)
  - the body of the class executes, the the namespace dictionary is populated with all the attributes, methods defined and additional useful info about the class
  - the metaclass is identified
  - the metaclass is then called with the name, bases and attributes of the class to instantiate it. 'type' is the default metaclass in Python
