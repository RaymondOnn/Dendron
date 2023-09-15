---
id: lpy8c36zkl9yv9lx618in42
title: Methods
desc: ''
updated: 1692224265294
created: 1692222625632
---


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

## Instance Methods
- Methods that refer to the instance of the class
- affects the instance of the class

## Static Methods
- Method that can be used anything since it does not refer to the class
- does not affect the instance nor the class

## Class Methods
- e.g. an alternative constructor using diff set of args
- changes how the class works