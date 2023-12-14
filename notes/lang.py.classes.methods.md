---
id: lpy8c36zkl9yv9lx618in42
title: methods
desc: ''
updated: 1701627606361
created: 1692222625632
---

### Method Types
### Instance Methods

- Methods that refer to the instance of the class
- affects the instance of the class

### Static Methods (`@staticmethod`)

- Method that can be used anything since it does not refer to the class
- does not affect the instance nor the class

### Class Methods (`@classmethod`)

- e.g. an alternative constructor using diff set of args
- changes how the class works

```py
class Calendar:
    def __init__(self):
        self.events = []
    
    def add_event(self, event):
        if type(self).is_weekend(event,date):
            raise ValueError("no")
        this.evemts.append(event)
    
    @staticmethod
    def is_weekend(date):
        return date.weekday()

    @classmethod
    def from json(cls, filename):
        c = cls()
        ...
        return c

class WorkCalendar(Calendar):
    pass

if __name__ == '__main__':


```

### Method Chaining
What is Method Chaining?
- Method Chaining is the technique of calling a method on another method and so on, of the same object. 
- method chaining is a technique where you combine individual methods in a single line of code to form a chain of actions. 
- Each method in the chain performs a specific function, but when combined together, they create a powerful sequence of actions that can accomplish complex tasks with just one line of code.

#### Implementation

``` py
class Calculator:
    def __init__(self):
        self.result = 0
        
    def add(self, num):
        self.result += num
        return self
    
    def subtract(self, num):
        self.result -= num
        return self
    
    def multiply(self, num):
        self.result *= num
        return self
    
    def divide(self, num):
        self.result /= num
        return self

# Method chaining in action
result = Calculator().add(10).multiply(2).subtract(1).divide(10).result
print(result) # Output: 1.9        
```