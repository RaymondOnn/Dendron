---
id: pzjl783zy1ac3fk1wyyfoqx
title: lazy_evaluation
desc: ''
updated: 1729315780643
created: 1704417687945
---

## What is Lazy Evaluation

- Lazy evaluation is a programming implementation paradigm that defers evaluating necessary operations until it’s requested to do so.
- Usually, lazy evaluation is the preferred implementation when the operation is expensive, requiring either extensive processing time or memory.
- For example, in Python, one of the best-known techniques involving lazy evaluation is generators. Instead of creating whole sequences for the iteration, which can consume lots of memory, generators lazily evaluate the current need and yield one element at a time when requested.

### An example

- Suppose we’re building a website that allows the user to interact with other users.
- One functionality is to view one’s followers, presented in a list.
- When we tap one user, we can view the user’s profile in a pop-up window. Let’s write some code to show a possible implementation.

    ```py
    # Define the needed data structure and helper functions
    class User:
        def __init__(self, username):
            self.username = username
            self.profile_data = self._get_profile_data()
            print(f"{self.__class__.__name__} instance created")

        def _get_profile_data(self):
            # get the data from the server and load it to memory
            print("Run the expensive operation")
            fetched_data = "The mock data of a large size"
            return fetched_data

    def get_followers(username):
        #get the followers from the server for the user
        usernames_fetched = ["David", "Aaron", "Zack"]
        users = [User(username) for username in usernames]
        return users

    # while the current implementation involves fetching the profile
    # data for all the users in the list, which can be expensive,
    # these profile data aren’t required for displaying the needed
    # user interface because the profile data are only used when a
    # particular username is tapped.

    users = get_followers("user0")
    >>> Run the expensive operation
    >>> User instance created
    >>> Run the expensive operation
    >>> User instance created
    >>> Run the expensive operation
    >>> User instance created
    ```

### Using `@property`

- Properties are decorated functions that provide an interface to ease data handling.
- Implementation is more straightforward and easier to understand

```py
# Updated User class with the property
class User1:
    def __init__(self, username):
        self.username = username
        self._profile_data = None
        print(f"{self.__class__.__name__} instance created")

    @property
    def profile_data(self):
        if self._profile_data is None:
            print("self._profile_data is None")
            # fetching of data executed only when required!!
            self._profile_data = self._get_profile_data()
        else:
            print("self._profile_data is set")
        return self._profile_data

    def _get_profile_data(self):
        # get the data from the server and load it to memory
        print("Run the expensive operation")
        fetched_data = "The mock data of a large size"
        return fetched_data

# Checking lazy implementation
def test_lazy_attr(how, cls):
    user0 = cls("John")
    print(f"* {how} First time of retrieving the profile_data")
    print(user0.profile_data)
    print(f"* {how} Second time of retrieving the profile_data")
    print(user0.profile_data)

test_lazy_attr("property", User1)

>>> User1 instance created
>>> * property First time of retrieving the profile_data
>>> self._profile_data is None
>>> Run the expensive operation
>>> The mock data of a large size
>>> * property Second time of retrieving the profile_data
>>> self._profile_data is set
>>> The mock data of a large size
```

### Using `__getattr__`

- With instance objects, their attributes saved in a dictionary, that can be accessed via `__dict__`.
- In this dictionary, the attribute names are stored as keys and the corresponding attribute values as its values.
- if the dictionary doesn’t contain the specified attribute, `__getattr__` will get called as a fallback mechanism.
- Better if there are multiple lazy attributes since it provides a centralized place to manage these attributes

```py
# Updated User class with the __getattr__
class User2:
    # Note: setting of self._profile_data is not in __init__() method
    # Vital that the attribute is not stored in __dict__, not even with value of nONE
    # else __getattr__ wont get called
    def __init__(self, username):
        self.username = username
        print(f"{self.__class__.__name__} instance created")

    def __str__(self):
        return f"user {self.username}"

    def __getattr__(self, name):
        print(f"__getattr__ called for {name}")
        if name == "profile_data":
            profile_data = self._get_profile_data()
            setattr(self, name, profile_data)
            return profile_data
        else:
            raise AttributeError(f"{self} has no attribute called {name}.")

    def _get_profile_data(self):
        # get the data from the server and load it to memory
        print("Run the expensive operation")
        fetched_data = "The mock data of a large size"
        return fetched_data

test_lazy_attr("getattr", User2)

>>> User2 instance created
>>> * getattr First time of retrieving the profile_data
>>> __getattr__ called for profile_data
>>> Run the expensive operation
>>> The mock data of a large size
>>> * getattr Second time of retrieving the profile_data
>>> The mock data of a large size

def test_lazy_attr_getattr():
    user2 = User2("Danny")
    print(f"* Before retrieving profile_data: {user2.__dict__}")
    print(user2.profile_data)
    print(f"* After retrieving profile_data: {user2.__dict__}")

test_lazy_attr_getattr()
>>> User2 instance created
>>> * Before retrieving profile_data: {'username': 'Danny'}
>>> __getattr__ called for profile_data
>>> Run the expensive operation
>>> The mock data of a large size
>>> * After retrieving profile_data: {'username': 'Danny', 'profile_data': 'The mock data of a large size'}
>>> The mock data of a large size

```

### Using `__getattribute__`: Possible but not recommended

- Unlike the `__getattr__` method, which doesn’t get called when a particular attribute is in the instance dictionary, the `__getattribute__` method **gets called every time an attribute is retrieved.**
- This feature is only useful when you expect the attribute to change very frequently and only the latest data are relevant. In these cases, we can achieve the effect by defining pertinent functions instead.
- Not recommended since it’s tricky to make it function properly (see [here](https://stackoverflow.com/questions/3278077/difference-between-getattr-and-getattribute) for a brief discussion). For e.g., one particular problem you may run into is infinite recursive loops — the `__getattribute__` method gets called an infinite number of times and will crash your program.
