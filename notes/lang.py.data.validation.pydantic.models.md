---
id: wz1jz1ir1nk3mprdrv62ahu
title: models
desc: ''
updated: 1712043898419
created: 1706973344147
---


### Models

- With pydantic, we can build a class to determine the shape of the data we are expecting, as well as the data-types for each field. 
- These classes are called models, and they inherit from a `BaseModel` class in pydantic.
-   They define the data structure using type annotations and offer avenues for data validation and conversion.

    ```py
    # Validation without `pydantic`
    class User:
        def __init__(self, id, name='Jane Doe'):
            if not isinstance(id, int):
                raise TypeError(f'Expected id to be an int, got {type(id).__name__}')

            if not isinstance(name, str):
                raise TypeError(f'Expected name to be a str, got {type(name).__name__}')

            self.id = id
            self.name = name

    try:
        user = User(id='123')
    except TypeError as e:
        print(e)
    >>> Expected id to be an int, got str

    # with Pydantic: No output if everything works -------------------
    from pydantic import BaseModel

    class User(BaseModel):
        id: int
        name: str = 'Jane Doe'
        
    user = User(id='123')
    print(user.id)
    ```
### Constrained Types

- A constraint on a field is essentially a limit on the potential values it can take on.
- For each Python primitive, pydantic has a constrained variant for e.g, for the `int` type, there's a `conint` type available. We also have `confloat`, `constr`, `conlist`, etc.
- Each of these constrained types have some arguments that can define the constraints i.e. enforce upper- or lower-case (strings), enforce lower/upper bounds on the number of items in a list/set/frozenset, and enforce lower/upper bounds on the values of numbers (ints, floats, Decimals).

    ``` py

    from pydantic import BaseModel, conint

    class User(BaseModel):
        name: str
        age: conint(gt=0, lt=130) # <--- constrained integer type

    # dummy data
    u1 = {'name': 'xyz', 'age': 50}
    u2 = {'name': 'xyz', 'age': 150}

    # attempt to convert to Pydantic models
    user1 = User(**u1)  # this is fine
    user2 = User(**u2)  # ValidationError - age in 'u2' exceeds upper bound
    ```
- Note: the same effect can be achieved using Pydantic's `Field` function.
    ``` py
    from pydantic import BaseModel, Field

    class User(BaseModel):
        name: str
        age: int = Field(..., gt=0, lt=130)
    # Field() is useful for specifying extra info and validation/constraints on fields.
    ```

### Model Enum fields
- We can define data types of fields as Enums. 

``` py
from enum import Enum

class DepartmentEnum(Enum):
    ARTS_AND_HUMANITIES = 'Arts and Humanities'
    LIFE_SCIENCES = 'Life Sciences'
    SCIENCE_AND_ENGINEERING = 'Science and Engineering'

# Using DepartmentEnum as our type for the department field:
class Student(BaseModel):
    id: uuid.UUID
    name: str
    date_of_birth: date
    GPA: confloat(ge=0, le=4)
    course: str | None
    department: DepartmentEnum # <-- valid values restricted to those defined in the enum 
    fees_paid: bool
```


### Nested models

-   Nested Models allow you to represent complex, nested data structures by defining models within models.

```py
from typing import List, Optional
from pydantic import BaseModel


class Food(BaseModel):
    name: str
    price: float
    ingredients: Optional[List[str]] = None


class Restaurant(BaseModel):
    name: str
    location: str
    foods: List[Food].  <<< nested field here


restaurant_instance = Restaurant(
    name="Tasty Bites",
    location="123, Flavor Street",
    foods=[
        {"name": "Cheese Pizza", "price": 12.50, "ingredients": ["Cheese", "Tomato Sauce", "Dough"]},
        {"name": "Veggie Burger", "price": 8.99}
    ]
)

print(restaurant_instance)
print(restaurant_instance.model_dump())
```

### Helper Methods
-   Pydantic also provides helper method to work with the model
    -   To see all fields within the model
        ```py
        print(user.model_fields_set)
        user = User(id='123', name='Joe Doe')
        print(user.model_fields_set)
        >>> {'id'}
        >>> {'id', 'name'}
        ```
    -   To export the data in various formats
        ```py
        print(user.model_dump()) # to dict
        print(user.model_dump_json()) # complex data types incl objects serialized into str
        print(user.model_json_schema())
        ```

### Advanced data types

-   Can install pydantic extensions for access to more advanced data types

    ```py
    !pip install pydantic[email]
    ```

-   for e.g. with `pydantic[email]`, we can data types such as `EmailStr`, `PositiveInt`, `HttpUrl`

```py

from typing import List
from pydantic import BaseModel, EmailStr, PositiveInt, conlist, Field, HttpUrl

class Address(BaseModel):
    street: str
    city: str
    state: str
    zip_code: str

class Employee(BaseModel):
    name: str
    position: str
    email: EmailStr

class Owner(BaseModel):
    name: str
    email: EmailStr

class Restaurant(BaseModel):
    name: str = Field(..., pattern=r"^[a-zA-Z0-9-' ]+$") # '...' implies required field
    owner: Owner
    address: Address
    employees: conlist(Employee, min_length=2) # con-strained list
    number_of_seats: PositiveInt
    delivery: bool
    website: HttpUrl

# Creating an instance of the Restaurant class
restaurant_instance = Restaurant(
    name="Tasty Bites",
    owner={
        "name": "John Doe",
        "email": "john.doe@example.com"
    },
    address={
        "street": "123, Flavor Street",
        "city": "Tastytown",
        "state": "TS",
        "zip_code": "12345",
    },
    employees=[
        {
            "name": "Jane Doe",
            "position": "Chef",
            "email": "jane.doe@example.com"
        },
        {
            "name": "Mike Roe",
            "position": "Waiter",
            "email": "mike.roe@example.com"
        }
    ],
    number_of_seats=50,
    delivery=True,
    website="http://tastybites.com"
)

# Printing the instance
print(restaurant_instance)
```