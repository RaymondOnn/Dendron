---
id: 9o5yv8bzyz25wkou9gtvltp
title: Polymorphism
desc: ''
updated: 1723197322157
created: 1723192333808
---

### What is PolyMorphism

### When should it be used?

Replacing conditionals with polymorphism

- Using subtype polymorphism when there is a variation in behaviour, not variation in data
- This makes the code simpler and more flexible
- With variations in data, you just need a regular class and different parameters

#### Example 1

``` mermaid
classDiagram
    class IAttack{
        +String Name
        +Int Damage   
    }
    class Thunderbolt {
        +String Name
        +Int Damage
    }
    
    class Scratch {
        +String Name
        +Int Damage
    }
    
    IAttack <|-- Thunderbolt
    IAttack <|-- Scratch
```

- Bad use of polymorphism since this is a variation in data
- Purpose of properties is to encapsulate data
  - To address the variation in data, we use different parameters
  - Better implementation is to just have a single class Attack and set different values for the properties for the different attacks
  - The different attacks should be objects, not classes

#### Example 2

``` mermaid
classDiagram
    class IAttack{
        +Use(Player target) none
    }
    class Thunderbolt {
        +Use(Player target) none
    }
    
    class Scratch {
        +Use(Player target) none
    }
    
    IAttack <|-- Thunderbolt
    IAttack <|-- Scratch

    note for Thunderbolt "Use() deducts target Health by 50"
    note for Scratch "Use() deducts target Health by 25"
```

- Bad use of polymorphism since this is a variation in data
- Better implementation is to just keep the `Attack` class but add a property to get the `damage` value
- The different attacks are just instance of the `Attack` class with different values set to the property `damage`

#### Example 3

``` mermaid
classDiagram
    class IMove{
        +Use(Player target) none
    }
    class Attack {
        +Use(Player target) none
    }
    
    class Heal {
        +Use(Player target) none
    }
    
    IMove <|-- Attack
    IMove <|-- Heal

    note for Attack "Use() deducts target Health by X"
    note for Heal "Use() resets target Health to 100 and resets target Armor to 100"
```

- Proper use of polymorphism since this is a variation in behaviour
- Difference in behaviours
  - Access different properties: `Heal` accesses an additional property `Armor`
  - Uses different algorithms: deducting vs resetting
  - `Attack` is used on another player, `Heal` is used on oneself

> **Food for thought: Can Example 3 be implemented without subtype polymorphism?**
>
> Yes, but not easy.
> We need need to use conditionals, which makes the code more complicated and less flexible.
