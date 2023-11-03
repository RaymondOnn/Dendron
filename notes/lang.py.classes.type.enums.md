---
id: jenhhz36aquteh4p32u3qqe
title: enums
desc: ''
updated: 1697526974800
created: 1697518532690
---


## Enums

- Enum is useful when you need immutable name-value pairs enclosed in an iterable object.
- Useful when you want to pick values from a list of constants

``` py
# Without Enums
def create_total_spend_column(
    df: pd.DataFrame,
    target_department: Literal[
        "finance",
        "human_resources",
        "sales",
        "research",
        "data_analytics",
        "engineering",
        "marketing",
    ],
) -> pd.DataFrame:
    """Sums columns for a target_department"""
    target_cols = [col for col in df.columns if target_department in col]
    df[f"Total {target_department}"] = df[target_cols].sum(axis=1)
    return df


# With Enums
# Here we are able to reuse the same list of constants elsewhere
from enum import Enum

class Departments(Enum):
    FINANCE = "finance"
    HR = "human_resources"
    SALES = "sales"
    RESEARCH = "research"
    DATA_ANALYTICS = "data_analytics"
    ENGINEERING = "engineering"
    MARKETING = "marketing"


def create_total_spend_column(
    df: pd.DataFrame,
    target_department: Departments,
) -> pd.DataFrame:
    """Sums columns for a target_department"""
    target_cols = [col for col in df.columns if target_department.value in col]
    df[f"Total {target_department}"] = df[target_cols].sum(axis=1)
    return df


df = create_total_spend_column(df, Departments.SALES)

```

### [Working with Enums](https://betterprogramming.pub/take-advantage-of-the-enum-class-to-implement-enumerations-in-python-1b65b530e1d)
- https://python.plainenglish.io/meet-enums-the-hidden-power-of-python-7fc5621a4a89
#### Declare an Enumeration Class

```py
from enum import Enum

class Direction(Enum):
    NORTH = 1
    EAST = 2
    SOUTH = 3
    WEST = 4
```

#### Use and Check Individual Members

- These attributes are considered as members of the enumeration. Hence we can use the introspection functions (e.g. type and isinstance).
- For each member, it has additional attributes such as name and value, which represent the enumerated member’s name and its associated value

``` py
print(Direction.NORTH)
print("Type:", type(Direction.SOUTH))
print("Check Instance:", isinstance(Direction.EAST, Direction))
>>> Direction.NORTH
>>> Type: <enum 'Direction'>
>>> Check Instance: True

>>> # Create a member
... north = Direction.NORTH
... # Check the name and the value
... print("Name:", north.name)
... print("Value:", north.value)
... 
Name: NORTH
Value: 1


>>> # Create a member from an integer value
... fetched_direction_value = 2
... fetched_direction = Direction(fetched_direction_value)
... print("Fetched Direction:", fetched_direction)
... # Condition evaluations
... print("Is the fetched direction north?", fetched_direction is Direction.NORTH)
... print("Is the fetched direction east?", fetched_direction is Direction.EAST)
... 
Fetched Direction: Direction.EAST
Is the fetched direction north? False
Is the fetched direction east? True
```

#### Iterate Members

``` py
>>> for direction in Direction:
...     print(direction)
... 
Direction.NORTH
Direction.EAST
Direction.SOUTH
Direction.WEST


# __members__: retrieve the mapping of the enum as key-value pairs.
>>> for name, direction in Direction.__members__.items():
...     print(f"* Name: {name:<5}; * Direction: {direction}")
... 
* Name: NORTH; * Direction: Direction.NORTH
* Name: EAST ; * Direction: Direction.EAST
* Name: SOUTH; * Direction: Direction.SOUTH
* Name: WEST ; * Direction: Direction.WEST
```

#### Define Methods

- Although the base Enum class is different from other regular classes because it specially handles its attributes as members, we can still define methods for the class like other classes.

``` py
>>> class Direction(Enum):
...     NORTH = 1
...     EAST = 2
...     SOUTH = 3
...     WEST = 4
... 
...     def angle(self):
...         right_angle = 90.0
...         return right_angle * (self.value - 1)
... 
...     @staticmethod
...     def angle_interval(direction0, direction1):
...         return abs(direction0.angle() - direction1.angle())
... 
... 
... east = Direction.EAST
... print("South Angle:", east.angle())
... west = Direction.WEST
... print("Angle Interval:", Direction.angle_interval(east, west))
... 
South Angle: 90.0
Angle Interval: 180.0
```

#### Functional APIs

- We can also create enums by calling a function.

``` py

>>> # Create an Enum class using the functional API
# An example of a factory function
... DirectionFunctional = Enum("DirectionFunctional", "NORTH EAST SOUTH WEST")
... # Check what the Enum class is
... print(DirectionFunctional)
... # Check the items
... print(list(DirectionFunctional))
... print(DirectionFunctional.__members__.items())
... 
<enum 'DirectionFunctional'>
[<DirectionFunctional.NORTH: 1>, <DirectionFunctional.EAST: 2>, <DirectionFunctional.SOUTH: 3>, <DirectionFunctional.WEST: 4>]
dict_items([('NORTH', <DirectionFunctional.NORTH: 1>), ('EAST', <DirectionFunctional.EAST: 2>), ('SOUTH', <DirectionFunctional.SOUTH: 3>), ('WEST', <DirectionFunctional.WEST: 4>)])
```

#### Defining functions for enums via monkey patching

##### Monkey patching

A technique that allows you to create and/or modify the behavior of existing modules or classes during runtime.

``` py
>>> # Create a function and patch it to the DirectionFunctional class
... def angle(DirectionFunctional):
...     right_angle = 90.0
...     return right_angle * (DirectionFunctional.value - 1)
... 
... 
... DirectionFunctional.angle = angle
... 
... # Create a member and access its angle
... south = DirectionFunctional.SOUTH
... print("South Angle:", south.angle())
... 
South Angle: 180.0
```

---

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
