---
id: r05qcxrudgu4pfuklxf62d8
title: pydantic
desc: ''
updated: 1707602148605
created: 1706882035626
---

https://www.youtube.com/watch?v=7aBRk_JP-qY

## `pydantic`

### what is `pydantic`

-   pydantic is a Python library that handles data validation and settings management using type-annotated class-fields. 

### Getting Started

-   To install pydantic:

```py
import pydantic
print(pydantic.__version__)
```



### Integration with Dataclasses

-   Pydantic offers integrations with Python Dataclasses to simplify type safety and data validation.

    ```py
    from dataclasses import dataclass, field
    from typing import List, Optional
    from pydantic import Field, TypeAdapter


    @dataclass
    class User:
        id: int
        name: str = 'John Doe'
        age: Optional[int] = field(
            default=None,
            metadata=dict(title='The age of the user', description='do not lie!', ge=18),
        )
        height: Optional[int] = Field(None, title='The height in cm', ge=50, le=300)
        friends: List[int] = field(default_factory=lambda: [0])
    ```

#### TypeAdapter

-   You may have types that are not BaseModels that you want to validate data against. Or you may want to validate a List[SomeModel], or dump it to JSON.
-   For use cases like this, Pydantic provides TypeAdapter, which can be used for type validation, serialization, and JSON schema generation without creating a BaseModel.

    ```py
    # Example of using TypeAdapter to get json_schema of the User dataclass
    print(TypeAdapter(User).json_schema())
    ```

### Strict mode

-   Pydantic, by default, tries to coerce values to the declared type, converting inputs like the string "123" to integer 123, but this automatic conversion can be undesirable in situations where strict type compliance is required, causing a need for configurations to make Pydantic error out instead.

```py
from pydantic import BaseModel, ValidationError


class User(BaseModel):
    id: int
    username: str


print(User.model_validate({'id': '42', 'username': 'john_doe'}))  # lax mode
#> id=42 username='john_doe'
try:
    User.model_validate({'id': '42', 'username': 'john_doe'}, strict=True)  # strict mode
except ValidationError as exc:
    print(exc)
```
