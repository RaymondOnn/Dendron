---
id: hhlnigrcs62qsa3nwgq8ro5
title: extending_enums
desc: ''
updated: 1742746704247
created: 1742745788395
---

## [Extending Enums](https://python.plainenglish.io/the-enum-trick-every-python-developer-should-know-6d3b0d0ef351)

### Adding Behavior to Enums

- Cleaner and easier to maintain.

``` py
# Instead of repeating role checks all over the place, I moved the logic into the Enum itself:
# if a new role needed edit permissions? Just update the Enum.
class UserRole(Enum):
    ADMIN = 1
    EDITOR = 2
    VIEWER = 3

    def can_edit(self):
        return self in {UserRole.ADMIN, UserRole.EDITOR}

    def can_delete(self):
        return self == UserRole.ADMIN
        
# Before, instead of writing:
if role in [UserRole.ADMIN, UserRole.EDITOR]:
    print("User can edit content")

# After
if role.can_edit():
    print("User can edit content")
```

### Auto-Assigning Values

``` py
from enum import Enum, auto

# Automatically assigns 1, 2, 3 to the roles
class UserRole(Enum):
    ADMIN = auto()
    EDITOR = auto()
    VIEWER = auto()
```

### Enums with Extra Data

At some point, I realized I needed more than just a name — roles had different permissions and descriptions.

``` py
# Instead of managing separate dictionaries, I embedded this info inside the Enum:
# Everything stays together, making the code easier to read and extend.
class UserRole(Enum):
    ADMIN = ("Admin", "Has full access")
    EDITOR = ("Editor", "Can edit content")
    VIEWER = ("Viewer", "Can only view content")

    def __init__(self, display_name, description):
        self.display_name = display_name
        self.description = description

# Direct access to role details:
role = UserRole.ADMIN
print(role.display_name)  # Admin
print(role.description)  # Has full access
```

### Improve Readability

``` py
# By default, printing an Enum looks like this:
print(UserRole.ADMIN)  # UserRole.ADMIN

# It’s fine, but if I wanted something cleaner, I could override the __str__ method:
class UserRole(Enum):
    ADMIN = auto()
    EDITOR = auto()
    VIEWER = auto()

    def __str__(self):
        return self.name.capitalize()

print(UserRole.ADMIN)  # Admin
```

### Enums and Databases

If you’re storing Enums in a database, use strings instead of numbers.

``` py
# This way, instead of saving 1 in the database, you store "admin", which makes debugging and querying much easier.
class UserRole(Enum):
    ADMIN = "admin"
    EDITOR = "editor"
    VIEWER = "viewer"
```

Performance?

- Enums are slightly slower, but the difference is tiny.
- Unless you’re writing high-frequency performance-critical code, it’s not an issue. The readability and maintainability benefits far outweigh the performance cost.

``` py
from enum import Enum
import timeit

class RoleEnum(Enum):
    ADMIN = 1
    EDITOR = 2
    VIEWER = 3

class RoleClass:
    ADMIN = 1
    EDITOR = 2
    VIEWER = 3

print(timeit.timeit(lambda: RoleEnum.ADMIN, number=10**6))  # ~0.14s
print(timeit.timeit(lambda: RoleClass.ADMIN, number=10**6))  # ~0.12s
```
