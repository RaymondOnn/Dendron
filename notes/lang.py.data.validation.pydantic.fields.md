---
id: nf38ztufntcl77qupkkwhk9
title: fields
desc: ''
updated: 1706969112851
created: 1706969107713
---


### Fields
- Used to customize and add metadata to fields of models
- Fields are the individual variables defined within a model. They can have additional configurations and validations beyond the basic type annotations.

``` py
# default value
from pydantic import BaseModel, Field

class User(BaseModel):
    name: str = Field(default='John Doe') # set default value

user = User()
print(user)

# default functions
from uuid import uuid4
from pydantic import BaseModel, Field

class User(BaseModel):
    id: int = Field(default_factory=lambda: uuid4().hex) # auto generates uuid

user = User()
print(user)
```
#### Field aliases
- For validation and serialization, you can define an alias for a field.
- There are three ways to define an alias:
  - `Field(..., alias='foo')`
  - `Field(..., validation_alias='foo')`
  - `Field(..., serialization_alias='foo')`

``` py
from pydantic import BaseModel, Field


class User(BaseModel):
    name: str = Field(..., alias='username')


user = User(username='johndoe')  
print(user)
print(user.model_dump(by_alias=True))
```

> ##### But why would you want to do it? For example
> - if you API and database fields differ, you only need one model

#### Field Constraints

``` py

from typing import List
from pydantic import BaseModel, Field, EmailStr
from decimal import Decimal

class User(BaseModel):
    username: str = Field(..., min_length=3, max_length=10, pattern=r'^\w+$')
    email: EmailStr = Field(...)
    age: int = Field(..., gt=0, le=120)
    height: float = Field(..., gt=0.0)
    is_active: bool = Field(True)
    balance: Decimal = Field(..., max_digits=10, decimal_places=2)
    favorite_numbers: List[int] = Field(..., min_items=1)
user_instance = User(
    username="john_doe",
    age=30,
    height=5.9,
    weight=160.5,
    email="john.doe@example.com",
    password="securepassword",
    balance=9999.99,
    favorite_numbers=[1,2,3]
)

print(user_instance)
```
#### Computed Fields

- Computed Fields are fields whose values are dynamically computed from the values of other fields.
``` py
from pydantic import BaseModel, computed_field
from datetime import datetime


class Person(BaseModel):
    name: str
    birth_year: int

    @computed_field
    @property
    def age(self) -> int:
        current_year = datetime.now().year
        return current_year - self.birth_year


print(Person(name="John Doe", birth_year=2000).model_dump())

```
``` py

from pydantic import BaseModel, ValidationError, field_validator
from datetime import datetime

class Person(BaseModel):
    name: str
    birth_year: int

    @property
    def age(self) -> int:
        current_year = datetime.now().year
        return current_year - self.birth_year

    @field_validator('birth_year')
    @classmethod
    def validate_age(cls, v: int) -> int:
        current_year = datetime.now().year
        if current_year - v < 18:
            raise ValueError('Person must be 18 years or older')
        return v

try:
    print(Person(name="John Doe", birth_year=2006))
except ValidationError as e:
    print(e)
```