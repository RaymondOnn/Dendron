---
id: 1pxovhicqv04bdtn6n3iqd9
title: Design_patterns
desc: ''
updated: 1681188548476
created: 1679865621530
---


## Design Patterns
<br>


### Behavioural  
Identify common communication patterns between objects and realize these pattern

- **Chain of responsibility**: Allow a request to pass down a chain of receivers until it is handled.
- **Command**: Encapsulate all info needed to perform an action within a command request
- Interpreter:
- **Iterator**: Sequentially access the elements of a collection
- **Mediator**: Reduce dependencies between objects by forcing them to depend on the mediator object only.
- **Memento**: Save and restore the previous state of an object 
- Null Object
- **Observer**: Notify change to multiple objects through a subscription mechanism
- **Publisher-Subscriber**:
- **Registry**
- Servant
- **Specification**
- **State**: 
- **Strategy**: Allow selecting different implementations at runtime that accomplish the same thing
- **Template**: Define different ways of doing of an standard set of steps(SOP) via derived classes
- **Visitor**:  Add new operations to existing classes without changing them. Apply the right action based on the types of the object and the visitor.
- Fluent Interface

<br>

### Creational
Create objects in a manner suitable to the situation
- **Borg**: Multiple class instances that share the same state
- **Builder**: Produce different variations of an object with the constructor using different receipes
- Dependency Injection: A class accepts the objects it requires from an injector instead of creating the objects directly.
- **Factory**: Provides an interface for creating **objects** in a superclass, but allows subclasses to alter the type of objects that will be created.
- **Abstract Factory** Provide an interface for creating **families of objects** without specifying their concrete classes.
- **Lazy Evaluation**: Delays the eval of an expr until its value is needed and avoids repeated evals.
- **Object Pool**: Recycles from a pool of pre-initialized objects instead of creating new ones
- **Prototype**: Creates new object instances by cloning prototype
- Singleton
- Multiton

<br>

### Structural
How different classes and objects are combined to form larger structures
- **Adapter**: Convert the interface of a class into another interface clients expect.
- **Bridge**: Split a large class or a set of closely related classes into two separate hierarchies—abstraction and implementation—which can be developed independently of each other.
- **Composite**: Compose objects into tree structures to represent whole-part hierarchies and treat individual objects and compositions uniformly.
- **Decorator**: Add behaviors to an object without affecting its class
- Delegation
- Extension
- **Facade**: Good for connecting with a complex system via a simplified interface.
- **Flyweight**: Use sharing common parts of state between multiple objects to support large numbers of fine-grained objects efficiently (Memory management).
- **Front Controller**: Provides a centralized entry point that controls and manages request handling.
- Marker
- Module
- **Proxy**: Provides a substitute for another object and controls access to that object
- Private Class Data:
- Twin
  
#### Others
- Delegation Pattern
- Blackboard
- Graph Search
- Listener
<br>

##### Reference:
- https://github.com/faif/python-patterns
- https://www.youtube.com/watch?v=v6tpISNjHf8&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=20
- https://refactoring.guru/
- https://sourcemaking.com/design_patterns/iterator
- https://www.gofpatterns.com/index.php
- https://en.wikipedia.org/wiki/Software_design_pattern

<br>

Architectural Patterns
- https://learn.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker



- 3-tier
- Pipe & Filters: 
- MVC: Separate internal representations of data from the ways it is presented to/accepted from the user
- Retry
- Circuit Pattern

<br>


ETL Patterns
- Write Audit Publish(WAP) Pattern
- Signal Table Pattern
https://www.linkedin.com/posts/eczachly_dataengineering-activity-6962992934698848256-Gnye/?trk=posts_directory