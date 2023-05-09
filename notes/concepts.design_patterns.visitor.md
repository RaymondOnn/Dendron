---
id: yty3fyehb44iakfaf34i54q
title: Visitor
desc: ''
updated: 1680527012268
created: 1680524650521
---

# Visitor
- Separate algorithms from the objects on which they operate. i.e different credit cards give different amounts of cashback on different type of expenses
  - Algorithms are stored on the different expense classes instead of the credit card classes
- The classic technique for recovering lost type information.
- Do the right thing based on the type of two objects. i.e. object implements the correct respoense based on itself and the visitor (expenses in this example)

<br>

``` python
import abc


class Element(metaclass=abc.ABCMeta):
    """
    Define an Accept operation that takes a visitor as an argument.
    """

    @abc.abstractmethod
    def accept(self, visitor):
        pass


class ConcreteElementA(Element):
    """
    Implement an Accept operation that takes a visitor as an argument.
    """

    def accept(self, visitor):
        visitor.visit_concrete_element_a(self)


class ConcreteElementB(Element):
    """
    Implement an Accept operation that takes a visitor as an argument.
    """

    def accept(self, visitor):
        visitor.visit_concrete_element_b(self)


class Visitor(metaclass=abc.ABCMeta):
    """
    Declare a Visit operation for each class of ConcreteElement in the
    object structure. The operation's name and signature identifies the
    class that sends the Visit request to the visitor. That lets the
    visitor determine the concrete class of the element being visited.
    Then the visitor can access the element directly through its
    particular interface.
    """

    @abc.abstractmethod
    def visit_concrete_element_a(self, concrete_element_a):
        pass

    @abc.abstractmethod
    def visit_concrete_element_b(self, concrete_element_b):
        pass


class ConcreteVisitor1(Visitor):
    """
    Implement each operation declared by Visitor. Each operation
    implements a fragment of the algorithm defined for the corresponding
    class of object in the structure. ConcreteVisitor provides the
    context for the algorithm and stores its local state. This state
    often accumulates results during the traversal of the structure.
    """

    def visit_concrete_element_a(self, concrete_element_a):
        pass

    def visit_concrete_element_b(self, concrete_element_b):
        pass


class ConcreteVisitor2(Visitor):
    """
    Implement each operation declared by Visitor. Each operation
    implements a fragment of the algorithm defined for the corresponding
    class of object in the structure. ConcreteVisitor provides the
    context for the algorithm and stores its local state. This state
    often accumulates results during the traversal of the structure.
    """

    def visit_concrete_element_a(self, concrete_element_a):
        pass

    def visit_concrete_element_b(self, concrete_element_b):
        pass


def main():
    concrete_visitor_1 = ConcreteVisitor1()
    concrete_element_a = ConcreteElementA()
    concrete_element_a.accept(concrete_visitor_1)


if __name__ == "__main__":
    main()
```




References:
- https://www.youtube.com/watch?v=UQP5XqMqtqQ&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=16