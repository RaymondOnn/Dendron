---
id: 7qa4m5wjg0677eextep9xwc
title: descriptors
desc: ''
updated: 1749372757359
created: 1749371510211
---
<https://python.plainenglish.io/mastering-descriptors-the-python-power-feature-i-ignored-for-too-long-317e1e0ce74b>

## Descriptors
### Attributes Are not Just Variables

``` py
class MyClass:
    @property
    def x(self):
        return 10

#  obj.x = 10. But how?
```

- A property is actually a descriptor object, with a method called __get__.
- By executing `obj.x`, Python was really calling property `.__get__()` behind the scenes.

#### An example

``` py
# A descriptor that logs every time I read or write an attribute.
class VerboseAttribute:
    def __init__(self, initial_value=None):
        self.value = initial_value

    def __get__(self, instance, owner):
        print(f"Accessing: {self.value}")
        return self.value

    def __set__(self, instance, value):
        print(f"Setting to: {value}")
        self.value = value

class MyObject:
    x = VerboseAttribute(5)

obj = MyObject()    # Accessing: 5
print(obj.x)        # 5
obj.x = 100         # Setting to: 100
print(obj.x)        # 100
```

### The Secret: Python Looks for `__get__`, `__set__`, and `__delete__`

- Python uses these methods in a specific order when you access attributes on a class.
  - `__get__(self, instance, owner)`
  - `__set__(self, instance, value)`
  - `__delete__(self, instance)`
- This means every time I do:

    ``` py
    obj.x
    obj.x = 42
    del obj.x
    ```

  - Python checks whether x is a descriptor, and if so, it delegates the operation to the descriptor methods.

### Building a Type-Enforcing Descriptor

- An example for enforcing type checking on fields, like this:

    ```py
    class Typed:
        def __init__(self, name, expected_type):
            self.name = name
            self.expected_type = expected_type

        def __get__(self, instance, owner):
            return instance.__dict__[self.name]

        def __set__(self, instance, value):
            if not isinstance(value, self.expected_type):
                raise TypeError(f"Expected {self.expected_type}")
            instance.__dict__[self.name] = value

    class Person:
        name = Typed("name", str)
        age = Typed("age", int)

        def __init__(self, name, age):
            self.name = name
            self.age = age

    p = Person("Ali", 21)
    p.age = "twenty"  # Raises error
    ```

### Using Descriptors to Store Values in an Encoded Way

- An example to decode values automatically

    ``` py
    import base64

    class EncodedField:
        def __init__(self, name):
            self.name = name

        def __get__(self, instance, owner):
            encoded = instance.__dict__.get(self.name)
            return base64.b64decode(encoded.encode()).decode() if encoded else None

        def __set__(self, instance, value):
            encoded = base64.b64encode(value.encode()).decode()
            instance.__dict__[self.name] = encoded

    # Usage:
    # Super clean and safe for internal storage without exposing raw logic in every class.
    class User:
        password = EncodedField("password")

        def __init__(self, password):
            self.password = password

    u = User("my_secret")
    print(u.password)  # Prints: my_secret
    print(u.__dict__)  # Shows the encoded password
    ```

### When Descriptors Shine

- Making custom ORM fields (StringField, IntegerField)
- Lazy loading (fetching an attribute from DB only on access)
- Logging or debugging access to critical values
- Caching expensive computations
- Creating smart properties with auto-validation
- Encryption/decryption of data behind the scenes
- Creating Reusable Descriptor Types

### A tiny descriptor framework for common patterns

``` py
class Field:
    def __init__(self, name=None):
        self.name = name

    def __set_name__(self, owner, name):
        if not self.name:
            self.name = name

    def __get__(self, instance, owner):
        return instance.__dict__.get(self.name)

    def __set__(self, instance, value):
        instance.__dict__[self.name] = value

class PositiveInt(Field):
    def __set__(self, instance, value):
        if not isinstance(value, int) or value <= 0:
            raise ValueError("Must be a positive integer")
        super().__set__(instance, value)

class Product:
    price = PositiveInt()

    def __init__(self, price):
        self.price = price
```
