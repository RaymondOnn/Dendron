---
id: if1fy3cqe7a1nc0id1930b1
title: Dependency_injection
desc: ''
updated: 1680358931776
created: 1680358318298
---

### Dependency Injection
- Passing the dependencies to the constructor of the dependent object while it's being instantiated
- Dependency Injection (DI) is a technique whereby one object supplies the dependencies (services) to another object (client).
- It allows to decouple objects: no need to change client code simply because an object it depends on needs to be changed to a different one. (Open/Closed principle)


<br>

- In the following example `time_provider` (service) is embedded into TimeDisplay (client).
- If such service performed an expensive operation you would like to substitute or mock it in tests.

``` python
class TimeDisplay(object):
    def __init__(self):
        self.time_provider = datetime.datetime.now
    def get_current_time_as_html_fragment(self):
        current_time = self.time_provider()
        current_time_as_html_fragment = "<span class=\"tinyBoldText\">{}</span>".format(current_time)
        return current_time_as_html_fragment
```

``` python

import datetime
from typing import Callable


class ConstructorInjection:
    def __init__(self, time_provider: Callable) -> None:
        self.time_provider = time_provider

    def get_current_time_as_html_fragment(self) -> str:
        current_time = self.time_provider()
        current_time_as_html_fragment = '<span class="tinyBoldText">{}</span>'.format(
            current_time
        )
        return current_time_as_html_fragment


class ParameterInjection:
    def __init__(self) -> None:
        pass

    def get_current_time_as_html_fragment(self, time_provider: Callable) -> str:
        current_time = time_provider()
        current_time_as_html_fragment = '<span class="tinyBoldText">{}</span>'.format(
            current_time
        )
        return current_time_as_html_fragment


class SetterInjection:
    """Setter Injection"""

    def __init__(self):
        pass

    def set_time_provider(self, time_provider: Callable):
        self.time_provider = time_provider

    def get_current_time_as_html_fragment(self):
        current_time = self.time_provider()
        current_time_as_html_fragment = '<span class="tinyBoldText">{}</span>'.format(
            current_time
        )
        return current_time_as_html_fragment


def production_code_time_provider() -> str:
    """
    Production code version of the time provider (just a wrapper for formatting
    datetime for this example).
    """
    current_time = datetime.datetime.now()
    current_time_formatted = f"{current_time.hour}:{current_time.minute}"
    return current_time_formatted


def midnight_time_provider() -> str:
    """Hard-coded stub"""
    return "24:01"


if __name__ == "__main__":
    time_with_ci1 = ConstructorInjection(midnight_time_provider)
    time_with_ci1.get_current_time_as_html_fragment()
    >>> '<span class="tinyBoldText">24:01</span>'
    
    time_with_ci2 = ConstructorInjection(production_code_time_provider)
    time_with_ci2.get_current_time_as_html_fragment()
    >>> '<span class="tinyBoldText">...</span>'
    
    time_with_pi = ParameterInjection()
    time_with_pi.get_current_time_as_html_fragment(midnight_time_provider)
    >>> '<span class="tinyBoldText">24:01</span>'
    
    time_with_si = SetterInjection()
    time_with_si.get_current_time_as_html_fragment()
    >>> Traceback (most recent call last):
    >>> ...
    >>> AttributeError: 'SetterInjection' object has no attribute 'time_provider'
    
    time_with_si.set_time_provider(midnight_time_provider)
    time_with_si.get_current_time_as_html_fragment()
    >>> '<span class="tinyBoldText">24:01</span>'
```

Reference
- https://www.youtube.com/watch?v=v6tpISNjHf8&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=20