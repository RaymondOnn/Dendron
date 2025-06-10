---
id: mq2ryqvettn357l3legnjvm
title: missing
desc: ''
updated: 1748789515779
created: 1748788440657
---

## [The `__missing__` property](https://blog.stackademic.com/pythons-hidden-superpower-missing-in-custom-dicts-0e3da148503c)

### What Is `__missing__`?

- `__missing__` is a special method you can define inside a subclass of dict.
- Whenever you try to access a key that doesn’t exist i.e. using square brackets (not .get()) — Python will call your `__missing__` method.
- You can then decide what to return, what to raise, or what side effects to trigger.
- In other words, you hook into the “key not found” behavior at the lowest level.
- It’s like creating your own version of defaultdict, but with more freedom.

### Why Use `__missing__`?

Let’s look at some examples of where `__missing__` becomes more elegant than other options:

- Cleaner Counters

    ```py
    class CounterDict(dict):
        def __missing__(self, key):
            return 0
    ```

  - Now `my_counter['foo'] += 1` won’t raise an error — even if 'foo' never existed.

- Nested Structures

    ``` py
    # build a deeply nested dictionary without checking every level
    class NestedDict(dict):
        def __missing__(self, key):
            self[key] = NestedDict()
            return self[key]
    ```

- Logging Access Attempts

    ``` py
    # Track who’s asking for what — especially in config-heavy apps.
    class LoggingDict(dict):
        def __missing__(self, key):
            print(f"[LOG] Missing key accessed: {key}")
            raise KeyError(key)
    ```

- When `defaultdict` Isn’t Enough
  - Sure, `collections.defaultdict` is great for simple defaults.
  - But it has limitations:
    - You can’t make defaults vary based on the missing key.
    - You can’t log or raise or trigger side effects.
    - You can’t stop the key from being added after access.
  - With `__missing__`, you’re in full control.

- It’s like writing your own little interpreter for what “missing” means — customized to your domain logic.
