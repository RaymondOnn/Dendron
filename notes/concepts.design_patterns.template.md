---
id: iyfpxe2cq9besyztatd71ft
title: Template
desc: ''
updated: 1680528085080
created: 1680527871966
---
# Template
- Different subclass have different ways of doing the same set of operations
- Define the skeleton of an algorithm in an operation, deferring some steps to client subclasses. 
- Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure.
- Base class declares algorithm 'placeholders', and derived classes implement the placeholders.

<br>

``` python
import abc


class AbstractClass(metaclass=abc.ABCMeta):
    """
    Define abstract primitive operations that concrete subclasses define
    to implement steps of an algorithm.
    Implement a template method defining the skeleton of an algorithm.
    The template method calls primitive operations as well as operations
    defined in AbstractClass or those of other objects.
    """

    def template_method(self):
        self._primitive_operation_1()
        self._primitive_operation_2()

    @abc.abstractmethod
    def _primitive_operation_1(self):
        pass

    @abc.abstractmethod
    def _primitive_operation_2(self):
        pass


class ConcreteClass(AbstractClass):
    """
    Implement the primitive operations to carry out
    subclass-specificsteps of the algorithm.
    """

    def _primitive_operation_1(self):
        pass

    def _primitive_operation_2(self):
        pass


def main():
    concrete_class = ConcreteClass()
    concrete_class.template_method()


if __name__ == "__main__":
    main()
    ```

References:
- https://www.youtube.com/watch?v=t0mCrXHsLbI&t=200s
- https://sourcemaking.com/design_patterns/template_method
- https://refactoring.guru/design-patterns/template-method