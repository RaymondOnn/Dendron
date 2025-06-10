---
id: 5lqi1if9mbs9bfdrgnuhmsa
title: metaclasses
desc: ''
updated: 1749374613389
created: 1749373479600
---

<https://python.plainenglish.io/python-metaclasses-the-day-i-broke-my-brain-and-rebuilt-it-smarter-7c0c9cc3cb52>

### The Moment Everything Changed

- Let me start with a simple example:

    ``` py
    MyClass = type('MyClass', (), {'x': 5})
    print(MyClass)         # <class '__main__.MyClass'>
    print(MyClass().x)     # 5
    ```

- That one line `type(...)` actually creates a class.
- This means that classes are objects, created by calling a class.
- What class actually creates a class is `type` — the default metaclass in Python.

### `type` Creates other Classes

- When a class is defined:

    ``` py
    class Dog:
        sound = "bark"
    ```

  - Python internally does this:

      ``` py
      Dog = type('Dog', (), {'sound': 'bark'})
      ```

- if `type` builds classes, can I override it? Yes. That’s exactly what metaclasses do.

#### An example: Auto register plugin classes

- To auto-register any class I created, for example in a plugin system.

    ``` py
    class PluginMeta(type):
        plugins = []

        def __new__(cls, name, bases, dct):
            new_cls = super().__new__(cls, name, bases, dct)
            if name != 'BasePlugin':
                cls.plugins.append(new_cls)
            return new_cls

    Usage:

    class BasePlugin(metaclass=PluginMeta):
        pass

    class PluginA(BasePlugin):
        pass

    class PluginB(BasePlugin):
        pass

    print(PluginMeta.plugins) # prints a list of plugin classes
    ```

#### Injecting Behavior Into Classes Automatically

- I wanted every model class in my system to have a `save()` method.
- Normally, I’d define a base class with save, but with metaclasses:

    ``` py
    class AutoSaveMeta(type):
        def __new__(cls, name, bases, dct):
            if 'save' not in dct:
                def save(self):
                    print(f"Saving {self}")
                dct['save'] = save
            return super().__new__(cls, name, bases, dct)

    class User(metaclass=AutoSaveMeta):
        def __init__(self, name):
            self.name = name

    u = User("Ali")
    u.save()
    ```

- The `save()` was not defined in `User`, but was automatically injected

#### Enforcing Rules at Class Definition Time

- In order to ensure certain attributes were always defined in classes via metaclasses:

    ``` py
    class RequireFieldsMeta(type):
        def __new__(cls, name, bases, dct):
            if name != 'BaseModel' and 'fields' not in dct:
                raise TypeError(f"{name} must define 'fields'")
            return super().__new__(cls, name, bases, dct)

    class BaseModel(metaclass=RequireFieldsMeta):
        pass

    class Product(BaseModel):
        fields = ['id', 'name']

    # This will raise error
    # class InvalidModel(BaseModel):
    #     pass
    ```

- This means I am now able to prevent developer errors before they happen.

#### SQL Table Generator

Later, I created a mini ORM-like system where class attributes defined table fields, and the metaclass generated SQL schemas.

``` py
class SQLModelMeta(type):
    def __new__(cls, name, bases, dct):
        fields = []
        for key, value in dct.items():
            if isinstance(value, tuple):
                fields.append(f"{key} {value[0]}")
        dct['_schema'] = f"CREATE TABLE {name.lower()} ({', '.join(fields)});"
        return super().__new__(cls, name, bases, dct)

class User(metaclass=SQLModelMeta):
    id = ('INTEGER PRIMARY KEY',)
    name = ('TEXT',)
    email = ('TEXT',)

print(User._schema)  # OUTPUT: CREATE TABLE user (id INTEGER PRIMARY KEY, name TEXT, email TEXT);
```

### When Do I Really Use Metaclasses?

- Metaclasses are best when you need to:
  - Register classes automatically (plugin systems)
  - Inject common methods (like save, delete)
  - Validate structure at class definition time
  - Transform or enhance class definitions dynamically
  - Generate DSL-like behaviors from class definitions
- They’re not for everyday use, but when your app grows big and class patterns emerge, they reduce boilerplate and enforce consistency.

### Combining Metaclasses and Inheritance

Eventually, I tried combining metaclasses via inheritance. One of my biggest lessons:

``` py
class MetaA(type): pass
class MetaB(type): pass

# This will raise TypeError
class Combined(metaclass=MetaA, MetaB): pass

# To combine multiple metaclasses, you must combine them into a single metaclass:
class MetaCombined(MetaA, MetaB): pass
```
