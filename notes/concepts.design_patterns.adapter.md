---
id: ubx0vikj8i0lx1a19cxjn09
title: Adapter
desc: ''
updated: 1680414633058
created: 1680414302717
---
### Adapter
- Convert the interface of a class into another interface clients expect. Adapter lets classes work together that couldn't otherwise because of incompatible interfaces.
- Wrap an existing class with a new interface.
- Impedance match an old component to a new system

#### Problem
- An "off the shelf" component offers compelling functionality that you would like to reuse, but its "view of the world" is not compatible with the philosophy and architecture of the system currently being developed.

``` python
"""
Convert the interface of a class into another interface clients expect.
Adapter lets classes work together that couldn't otherwise because of
incompatible interfaces.
"""

import abc


class Target(metaclass=abc.ABCMeta):
    """
    Define the domain-specific interface that Client uses.
    """

    def __init__(self):
        self._adaptee = Adaptee()

    @abc.abstractmethod
    def request(self):
        pass


class Adapter(Target):
    """
    Adapt the interface of Adaptee to the Target interface.
    """

    def request(self):
        self._adaptee.specific_request()


class Adaptee:
    """
    Define an existing interface that needs adapting.
    """

    def specific_request(self):
        pass


def main():
    adapter = Adapter()
    adapter.request()


if __name__ == "__main__":
    main()
```

References:
- https://sourcemaking.com/design_patterns/adapter
- https://www.youtube.com/watch?v=wA3keqCeKtM&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=17