---
id: zz9xkegaim7xzdzmr0m3iri
title: Clean_architecture
desc: ''
updated: 1694744643647
created: 1692211830065
---
<https://www.youtube.com/watch?v=C7MRkqP5NRI&t=856s&ab_channel=EuroPythonConference>

> ### What is Architecture?
>
> The **ART** and **SCIENCE** in which the components of a computer system are organised and integrated

> ### What is the meaning of "clean"?
>
> For each component, you know **where** it is, **what** it is and **why** it is in the system.

## The Clean Architecture

- A layered approach for a more civilized age
  - from outside in:
    - External Systems
    - Gateways
    - Use Cases
    - Entities

### The Golden Rule

 **Talk inward with simple structures (out to in), talk outwards through interfaces (in to out)**
    - Simple Structure: Data types that have been defined inside
    - Interface:

## Example

- Use Case: retrieve a list of items

```py
def item_list_use_case():
    pass
```

- Entitites: simple models that represent real items in business logic

```py
class Item:
    def __init__(self, code, price):
        self.code = code
        self.price = price

```

- External system: We want to build a web application
  - Incoming HTTP requests become a call and simple structures ()

```py
@blueprint.route('/items', methods=['GET'])
def items():
    items
```

---

## Goals for Software Architecture

- Simplicity
- Understandability
- Flexibility
- Testability
- Maintainability

## Clean Architecture

- Focus
  - User Centric
  - Focus on Immediate Business Value
  - Minimizes Cost
- Achieved through
  - Loosely Coupled
  - Framework Independent
  - UI Independent
- Not ideal if
  - Small project
  - Non-changeable requirements
  - Can't afford maintaining different layers
  - Costly Transitions

### Layers

- From outer to inner:
  - Framework & Drivers
  - Interface Adapters
  - Application Rules
  - Enterprice Business Rules
- NOTE: Dependencies must point inwards
  - Inner Layers know nothing about Outer Layers
  - Outer layers know things about Inner layers

### Enterprise Business Rules

- Constitues the domain logic
- Abstract and high level entities that rarely changes i.e. Users, Products

### Application Business Rules

- Define Use Cases i.e. how entities interact
- Responsible for the coordirnatin of data

### Interface Adapters

- Reponsible for:
  - Conversion of data
  - Presentation of data
  - Control of data
- Transform data based application business rules

### Framework & Drivers

- Responsible for external resources
- For e.g. UI, tools, devices, 3rd party libraries

### An Example

- Domain
  - contains all business logic
  - Model + Use Cases
- Data:
  - Implementation
- App:
  - Presetation 
  - Inputs