---
id: 3icax0v4l8atvgv9rtz26ht
title: Lazy_evaluation
desc: ''
updated: 1680407919725
created: 1680407494173
---

### Lazy Evaluation
- Delays the eval of an expr until its value is needed and avoids repeated evals.
- Well, it turns out if you procrastinate long enough, sometimes it turns out that the work never needed to be done after all!
- 
<br>

#### Example
``` python
import functools


class lazy_property:
    def __init__(self, function):
        self.function = function
        functools.update_wrapper(self, function)

    def __get__(self, obj, type_):
        if obj is None:
            return self
        val = self.function(obj)
        obj.__dict__[self.function.__name__] = val
        return val


def lazy_property2(fn):
    """
    A lazy property decorator.
    The function decorated is called the first time to retrieve the result and
    then that calculated result is used the next time you access the value.
    """
    attr = "_lazy__" + fn.__name__

    @property
    def _lazy_property(self):
        if not hasattr(self, attr):
            setattr(self, attr, fn(self))
        return getattr(self, attr)

    return _lazy_property


class Person:
    def __init__(self, name, occupation):
        self.name = name
        self.occupation = occupation
        self.call_count2 = 0

    @lazy_property
    def relatives(self):
        # Get all relatives, let's assume that it costs much time.
        relatives = "Many relatives."
        return relatives

    @lazy_property2
    def parents(self):
        self.call_count2 += 1
        return "Father and mother"


if __name__ == "__main__":
    Jhon = Person('Jhon', 'Coder')
    Jhon.name
    >>> 'Jhon'
    
    Jhon.occupation
    >>> 'Coder'
    
    # Before we access `relatives`
    sorted(Jhon.__dict__.items())
    >>> [('call_count2', 0), ('name', 'Jhon'), ('occupation', 'Coder')]

    Jhon.relatives
    >>> 'Many relatives.'

    # After we've accessed `relatives`
    sorted(Jhon.__dict__.items())
    >>> [('call_count2', 0), ..., ('relatives', 'Many relatives.')]

    Jhon.parents
    >>> 'Father and mother'

    sorted(Jhon.__dict__.items())
    >>> [('_lazy__parents', 'Father and mother'), ('call_count2', 1), ..., ('relatives', 'Many relatives.')]

    Jhon.parents
    >>> 'Father and mother'

    Jhon.call_count2
    >>> 1
```


References:
- https://www.youtube.com/watch?v=KniIeHiEzdo