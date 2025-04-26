---
id: vk5fgow755corpkyumni3qa
title: types
desc: ''
updated: 1735379511844
created: 1735374320452
---


# Practical Decorators

<!-- 
_class: invert 
_footer: ""
_paginate: false
-->

### "Observer Decorators"

- *Add code before/after wrapped code to observe but not change operation.*
- Logging function calls
- Timing/performance measurements

#### The Logging Decorator

```python
import logging
from collections.abc import Callable
from functools import wraps
from typing import ParamSpec, TypeVar

T, P = TypeVar("T"), ParamSpec("P")
LOG = logging.getLogger(__name__)


# --- DECORATOR ---
def log_call(func: Callable[P, T]) -> Callable[P, T]:
    """Decorates a function log calls to it and errors/returns from it.

    Args:
        func: Function to decorate.

    Returns:
        Wrapped function.

    Examples:
        >>> import logging
        >>> @log_call
        ... def my_function():
        ...     ...
    """

    @wraps(func)
    def wrapper(*args: P.args, **kwargs: P.kwargs) -> T:
        """Wrapped call, which handles logging."""

        LOG.info("--> Calling %s", func.__name__)

        try:
            result = func(*args, **kwargs)

        except:
            LOG.error("<!- ERROR occurred calling %s", func.__name__, exc_info=True)
            raise

        else:
            LOG.info("<-- %s returning %r", func.__name__, result)
            return result

    return wrapper


# --- END DECORATOR ---


@log_call
def my_test_function(is_fail: bool = False) -> bool:
    """Does a thing.

    Args:
        is_fail: Should we fail?. Defaults to False.

    Raises:
        RuntimeError: If is_fail was a truthy value.

    Returns:
        Always returns True.
    """
    LOG.info("Inside my_test_function, where is_fail=%r", is_fail)
    if is_fail:
        raise RuntimeError("I am a scary failure!")
    return True


if __name__ == "__main__":
    logging.basicConfig(
        level=logging.INFO, format="%(levelname).1s %(funcName)s: %(message)s"
    )

    my_test_function()
    my_test_function(0)
    my_test_function(is_fail=True)
```
---

### "Parameterized Decorators"

- *Provide input to the decorator and use that in the decorated code.*
- Categorizing objects
- Authorization controls/checks
- Limiting controls/checks


#### The Registration Decorator
- Using a class instance as the decorator instead of a function allows us to store parameters as part of the object's state, and mutate it as necessary.

```python
from collections import UserList
from collections.abc import Callable
from string import ascii_uppercase
from typing import ParamSpec

P = ParamSpec("P")


# --- DECORATOR ---
class CategoryCollection(UserList):
    """Groups functions with the same signature that define rules.

    Examples:
        >>> number_rules = CategoryCollection('number_rules')
        >>> @number_rules
        ... def multiple_of_5(i):
        ...     return i % 5 == 0
        >>> @number_rules
        ... def even(i):
        ...     return i % 2 == 0
        >>> number_rules.violates(2)
        ['multiple_of_5']
        >>> number_rules.violates(5)
        ['even']
        >>> number_rules.violates(10)
        []
    """

    def __init__(self, category: str):
        """Prepare a CategoryCollection for use.

        Args:
            category: Category name.
        """
        super().__init__()
        self.category = category

    def __call__(self, func: Callable[P, bool]) -> Callable[P, bool]:
        """Decorates a function to add it to the collection.

        Args:
            func: Function to add.

        Returns:
            The original function.
        """
        self.append(func)
        return func

    def violates(self, *args: P.args, **kwargs: P.kwargs) -> list[str]:
        """Find registered functions that return false for the input.

        Returns:
            List of function names that returned False.
        """
        return [f.__name__ for f in self if not f(*args, **kwargs)]


# --- END DECORATOR ---

grammar_rules = CategoryCollection("grammar_rules")


@grammar_rules
def starts_with_a_capital(stuff: str) -> int:
    """Doubles the input.

    Args:
        stuff: Input.

    Returns:
        Doubled value.
    """
    return stuff.strip()[0] in ascii_uppercase


@grammar_rules
def ends_with_punctuation(sentence: str) -> int:
    """Halves the input.

    Args:
        sentence: Input.

    Returns:
        Half the input.
    """
    return sentence.strip()[-1] in ".?!"


@grammar_rules
def i_is_capitalized(sentence: str) -> int:
    """Adds 3 to the input.

    Args:
        i: Input.

    Returns:
        Input with 3 added.
    """
    return not any(w == "i" for w in sentence.split(" "))


if __name__ == "__main__":
    for s in ("i am someone!", "what is punctuation?", "foodle dee doodle dee"):
        print(f'"{s}" violates: {", ".join(grammar_rules.violates(s))}')
```


#### The Locking Decorator
- Indicates that the function needs a specific lock, and handles acquiring and releasing the lock around the function call. 
- The parameters take advantage of namespace rules. 
  - When you try to access the contents of a name, Python first checks the local namespace. 
  - Failing that, it begins checking each parent namespace, 
    - returning the value from the first namespace where the name is defined, 
    - or raising a NameError if it gets beyond the global namespace and still has not found the name defined.


```python
import logging
import time
from functools import wraps
from threading import Lock, Thread

LOG = logging.getLogger(__name__)


def uses_lock(func=None, *, lock=None):
    if not lock:
        raise ValueError("You must specify a lock.")

    def wrapper(wfunc):
        @wraps(wfunc)
        def wait_for_lock(*args, **kwargs):
            LOG.info("--> Waiting for %d", id(lock))
            with lock:
                LOG.info("Calling %s", wfunc.__name__)
                result = wfunc(*args, **kwargs)
            LOG.info("<-- Released %d", id(lock))
            return result

        return wait_for_lock

    return wrapper(func) if func else wrapper


keyboard_lock = Lock()
mouse_lock = Lock()


@uses_lock(lock=keyboard_lock)
def type_a_message():
    LOG.info("Starting type_a_message")
    time.sleep(1)
    LOG.info("Returning from type_a_message.")


@uses_lock(lock=keyboard_lock)
def send_keys():
    LOG.info("Starting send_keys")
    time.sleep(3)
    LOG.info("Returning from send_keys.")


@uses_lock(lock=mouse_lock)
def move_mouse():
    LOG.info("Starting move_mouse")
    time.sleep(5)
    LOG.info("Returning from move_mouse.")


if __name__ == "__main__":
    logging.basicConfig(
        level=logging.INFO, format="%(levelname).1s %(threadName)s: %(message)s"
    )

    threads = [
        Thread(target=f, name=f"{f.__name__} #{i}")
        for i in range(1, 4)
        for f in (type_a_message, send_keys, move_mouse)
    ]
    LOG.info("Starting threads")
    for t in threads:
        t.start()

    LOG.info("Waiting for threads")
    while any(t.is_alive() for t in threads):
        pass

    LOG.info("Threads complete.")
```

---


### Mutating Decorators

- *Catch input or output, and either validate or change the type.*
- Change input/return values.
- Validate input/output values.

#### The Defaulter Decorator
- This decorator uses a syntax that I first saw using pytest. 
  - This particular syntax (having `func=None` and then requiring parameters to be keyword args) allows for usage both with or without parenthesis.

- Ensures that a value is always of an expected type, and always returns a value. 
- The decorator is configurable with the type, exceptions to catch, and default value. 
  - Great for extracting or calculating values from a data structure you do not control, and want to ensure that the function always return a value of the specified type.
- Also makes sure to log exceptions that it handles, so that debugging is easier.
```python
import logging
from functools import wraps
from collections.abc import Callable
from typing import Any, ParamSpec, TypeVar

T, P = TypeVar("T"), ParamSpec("P")
LOG = logging.getLogger(__name__)


# --- DECORATOR ---
def default_on_fail(
    func: Callable[P, T] = None,
    *,
    type_: type | None = str,
    exceptions: type[Exception] = Exception,
    default: Any = None,
) -> Callable[P, T]:
    """Decorates a function to handle data conversion and exceptions.

    Args:
        func: Function to decorate. Defaults to None.

    Keyword Args:
        type_: Type that the output should be converted to. Defaults to str.
        exceptions: Exception types to catch from call or conversion. Defaults to
            Exception.
        default: Default value in the event of an error. Defaults to None.

    Raises:
        ValueError: If type_ is not specified.

    Returns:
        _type_: Function wrapper.

    Examples:
        >>> @default_on_fail
        ... def get_session_id(payload):
        ...     return payload['session']
        >>> get_session_id([])
        >>> get_session_id({})
        >>> get_session_id({'session': 5})
        '5'
        >>> @default_on_fail(type_=int, default=-1)
        ... def get_session_id(payload):
        ...     return payload['session']
        >>> get_session_id([])
        -1
        >>> get_session_id({})
        -1
        >>> get_session_id({'session': 5})
        5
    """

    def wrapper(wfunc: Callable[P, T]) -> Callable[P, T]:
        """Decorates a function to convert output and handle errors.

        Args:
            wfunc: Function to wrap.

        Returns:
            Wrapped function.
        """

        @wraps(wfunc)
        def change_output(*args: P.args, **kwargs: P.kwargs) -> T:
            """Wrapped function call."""
            try:
                result = wfunc(*args, **kwargs)
            except exceptions:
                LOG.warning("Caught error executing %s", wfunc.__name__, exc_info=True)
                return default

            try:
                return type_(result)
            except exceptions as e:
                LOG.warning(
                    "Caught error converting %s to %s: %s: %s",
                    type(result).__name__,
                    type_.__name__,
                    type(e).__name__,
                    e,
                )
                return default

        return change_output

    return wrapper(func) if func else wrapper


# --- END DECORATOR ---


@default_on_fail(type_=int, default=1)
def get_session(payload) -> int:
    """Extracts the Session field from the payload.

    Args:
        payload: Decoded JSON payload.

    Returns:
        Session value.
    """
    return payload["session"]


if __name__ == "__main__":
    logging.basicConfig(
        level=logging.INFO, format="%(levelname).1s %(funcName)s: %(message)s"
    )

    LOG.info("--> %r\n", get_session({"session": 5}))
    LOG.info("--> %r\n", get_session({"session": "5"}))
    LOG.info("--> %r\n", get_session({"session": None}))
    LOG.info("--> %r\n", get_session(None))
```

### Pros and Cons

#### Pros

- Makes code very portable
- Reduces boilerplate
- When decorators' functions are understood, aid in readability.

#### Cons

- Gives no insight into code complexity behind the decorator.
- Can create a layer of indirection which can make debugging complicated.
- Parameterized decorators can be complicated to maintain.
