---
id: mx9pdozmes7a0o7ltfa17jc
title: Protocols
desc: ''
updated: 1697519862878
created: 1697519290055
---

## Protocols

### What are protocols
- A protocol is used to **define a set of rules and special methods** that a class **must implement** in order to be considered a certain type of object. 
- The Protocol class is a part of the typing module and is a means to describe an informal interface, laying out a specific set of methods, attributes, or behaviours that a class can choose to adopt. 
- Protocol does not constitute a rigid construct, but serves as a means to establish a shared understanding of how objects from various classes should respond when particular methods are invoked upon them. 
- Classes that implement the attributes and methods specified in the Protocol are considered to be “compatible” with the protocol, even if they do not inherit from it explicitly.
- Protocols in Python play a role in facilitating duck typing, a programming concept that emphasizes an object’s behavior rather than its specific type. When an object adheres to the methods specified by a protocol, it can be treated as if it inherently belongs to that protocol, irrespective of its original class or inheritance structure.

### Why protocols
- Allows us to enforce the presence of specific methods in different classes while keeping the code more flexible and maintainable by avoiding inheritance. 
- It also helps in providing clear contracts between components in a data engineering project, making the code easier to understand and extend. 

1. Clear Intent and Documentation: Protocols make the expected behavior of classes explicit. By defining a protocol, you clearly specify what methods or attributes are required for an object to fulfill a certain role. This serves as documentation for developers and helps communicate the intended usage of classes, and makes it easier for developers to understand how different components interact with each other.

2. Improved Static Type Checking: Protocols work seamlessly with static type checking tools like ‘mypy’. When you use protocols to define expected behaviors, static type checkers can analyze your code and ensure that objects conform to the specified protocols. This helps catch potential errors early in the development process.

3. Easier Refactoring and Evolution: Protocols provide a powerful tool for refactoring and evolving code. When you need to change the behavior of certain classes, you can ensure that they still adhere to the existing protocols, preserving the expected behavior for the rest of the codebase.

Furthermore, using protocols permits the use of Composition. This refers to the ‘has-a’ (Composition) instead of the ‘is-a’ (Inheritance) approach to Object Oriented Programming (OOP). Composition is generally preferable to inheritance as a code-reuse mechanism because it is a weaker form of coupling, which is another term for dependencies between entities. Since protocols are not tied to any specific class hierarchy, you can dynamically compose classes and objects that adhere to protocols. This allows for more flexible designs and enables you to combine behavior from different sources.

``` py
class Extractor(Protocol):
    def extract(self) -> List[str]:
        pass

class CSVExtractor:
    def __init__(self, filepath: str):
        self.filepath = filepath

    def extract(self) -> List[str]:
        with open(self.filepath, 'r') as file:
            return file.readlines()

class Transformer(Protocol):
    def transform(self, data: List[str]) -> List[str]:
        pass

class SimpleTransformer:
    def transform(self, data: List[str]) -> List[str]:
        return [line.strip().upper() for line in data]

def process_data(
    extractor: Extractor,
    transformer: Transformer
    ) -> None:
    data = extractor.extract()
    transformed_data = transformer.transform(data)
    # Do something with the transformed data (e.g.,
    #  write to a file or database).

# Usage:
csv_extractor = CSVExtractor("data.csv")
simple_transformer = SimpleTransformer()

process_data(csv_extractor, simple_transformer)
```