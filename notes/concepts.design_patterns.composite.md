---
id: hxn1mwscpp9e9vh0iaaneku
title: Composite
desc: ''
updated: 1680421842666
created: 1680419483186
---

### Composite
- The composite pattern describes a group of objects that is treated the
same way as a single instance of the same type of object. 
- The intent of a composite is to "compose" objects into tree structures to represent
part-whole hierarchies. 
- Implementing the composite pattern lets clients treat individual objects and compositions uniformly.

<br>


``` python
import abc


class Component(metaclass=abc.ABCMeta):
    """
    Declare the interface for objects in the composition.
    Implement default behavior for the interface common to all classes,
    as appropriate.
    Declare an interface for accessing and managing its child
    components.
    Define an interface for accessing a component's parent in the
    recursive structure, and implement it if that's appropriate
    (optional).
    """

    @abc.abstractmethod
    def operation(self):
        pass


class Composite(Component):
    """
    Define behavior for components having children.
    Store child components.
    Implement child-related operations in the Component interface.
    """

    def __init__(self):
        self._children = set()

    def operation(self):
        for child in self._children:
            child.operation()

    def add(self, component):
        self._children.add(component)

    def remove(self, component):
        self._children.discard(component)


class Leaf(Component):
    """
    Represent leaf objects in the composition. A leaf has no children.
    Define behavior for primitive objects in the composition.
    """

    def operation(self):
        pass


def main():
    leaf = Leaf()
    composite = Composite()
    composite.add(leaf)
    composite.operation()


if __name__ == "__main__":
    main()
```

Reference:
- https://www.youtube.com/watch?v=oo9AsGqnisk&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=19
- https://sourcemaking.com/design_patterns/composite