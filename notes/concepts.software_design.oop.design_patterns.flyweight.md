---
id: io78w389ndh0zt3hfddqin8
title: Flyweight
desc: ''
updated: 1680422277667
created: 1680422143595
---

### Flyweight
- This pattern aims to minimise the number of objects that are needed by
a program at run-time. 
A Flyweight is an object shared by multiple contexts, and is indistinguishable from an object that is not shared.
- The state of a Flyweight should not be affected by it's context, this
is known as its intrinsic state. 
- The decoupling of the objects state from the object's context, allows the Flyweight to be shared.

<br>

``` python
import abc


class FlyweightFactory:
    """
    Create and manage flyweight objects.
    Ensure that flyweights are shared properly. When a client requests a
    flyweight, the FlyweightFactory object supplies an existing instance
    or creates one, if none exists.
    """

    def __init__(self):
        self._flyweights = {}

    def get_flyweight(self, key):
        try:
            flyweight = self._flyweights[key]
        except KeyError:
            flyweight = ConcreteFlyweight()
            self._flyweights[key] = flyweight
        return flyweight


class Flyweight(metaclass=abc.ABCMeta):
    """
    Declare an interface through which flyweights can receive and act on
    extrinsic state.
    """

    def __init__(self):
        self.intrinsic_state = None

    @abc.abstractmethod
    def operation(self, extrinsic_state):
        pass


class ConcreteFlyweight(Flyweight):
    """
    Implement the Flyweight interface and add storage for intrinsic
    state, if any. A ConcreteFlyweight object must be sharable. Any
    state it stores must be intrinsic; that is, it must be independent
    of the ConcreteFlyweight object's context.
    """

    def operation(self, extrinsic_state):
        pass


def main():
    flyweight_factory = FlyweightFactory()
    concrete_flyweight = flyweight_factory.get_flyweight("key")
    concrete_flyweight.operation(None)


if __name__ == "__main__":
    main()
```

