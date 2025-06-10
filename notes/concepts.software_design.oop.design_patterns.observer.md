---
id: b57axvy2ul1q8vu8gheybqt
title: observer
desc: ''
updated: 1748788493944
created: 1680349170488
---

### What is the Observer pattern

- The observer pattern is a design pattern where an object (the subject) maintains a list of dependents (observers) that are notified of state changes.
- In Python, this is often implemented through an event system where components can subscribe to events without needing direct knowledge of the components that trigger them.
- Key characteristics of this pattern include:
  - Loose coupling: Components interact without direct references to each other
  - One-to-many relationships: A single event can notify multiple listeners
  - Plug-and-play architecture: New observers can be added without modifying existing code

``` mermaid
classDiagram
    direction LR
    class IObservable{
        <<Interface>>
        +register(IObserver) None
        +remove(IObserver) None
        +notify() None
    }
    
    class ConcreteObservable{
        +register(IObserver) None
        +remove(IObserver) None
        +notify()

        + getState()
    }
    
    class ConcreteObserver {
        +update() None
    }
    class IObserver {
        <<Interface>>
        +update() None
    }
    
    
    IObservable "1" --> "1..*" IObserver
    ConcreteObservable --|> IObservable
    IObserver <|-- ConcreteObserver
    ConcreteObserver --> ConcreteObservable: references

```

<br>

``` python

from __future__ import annotations

from contextlib import suppress
from typing import Protocol


# define a generic observer type
class Observer(Protocol):
    def update(self, subject: Subject) -> None:
        pass


class Subject:
    def __init__(self) -> None:
        self._observers: list[Observer] = []

    def attach(self, observer: Observer) -> None:
        if observer not in self._observers:
            self._observers.append(observer)

    def detach(self, observer: Observer) -> None:
        with suppress(ValueError):
            self._observers.remove(observer)

    def notify(self, modifier: Observer | None = None) -> None:
        for observer in self._observers:
            if modifier != observer:
                observer.update(self)


class Data(Subject):
    def __init__(self, name: str = "") -> None:
        super().__init__()
        self.name = name
        self._data = 0

    @property
    def data(self) -> int:
        return self._data

    @data.setter
    def data(self, value: int) -> None:
        self._data = value
        self.notify()


class HexViewer:
    def update(self, subject: Data) -> None:
        print(f"HexViewer: Subject {subject.name} has data 0x{subject.data:x}")


class DecimalViewer:
    def update(self, subject: Data) -> None:
        print(f"DecimalViewer: Subject {subject.name} has data {subject.data}")


if __name__ == "__main__":
    data1 = Data('Data 1')
    data2 = Data('Data 2')
    
    view1 = DecimalViewer()
    view2 = HexViewer()
    
    data1.attach(view1)
    data1.attach(view2)
    data2.attach(view2)
    data2.attach(view1)
    
    data1.data = 10
    >>> DecimalViewer: Subject Data 1 has data 10
    >>> HexViewer: Subject Data 1 has data 0xa
    
    data2.data = 15
    >>> HexViewer: Subject Data 2 has data 0xf
    >>> DecimalViewer: Subject Data 2 has data 15
    
    data1.data = 3
    >>> DecimalViewer: Subject Data 1 has data 3
    >>> HexViewer: Subject Data 1 has data 0x3
    
    data2.data = 5
    >>> HexViewer: Subject Data 2 has data 0x5
    >>> DecimalViewer: Subject Data 2 has data 5
    
    # Detach HexViewer from data1 and data2
    data1.detach(view2)
    data2.detach(view2)
    
    data1.data = 10
    >>> DecimalViewer: Subject Data 1 has data 10
    
    data2.data = 15
    >>> DecimalViewer: Subject Data 2 has data 15
```

### [Example: Observer Pattern in a Data Engineering ETL Job](https://pravash-techie.medium.com/python-observer-pattern-to-cut-your-python-codebase-complexity-9ac2c0084d4a)

- Here’s a simplified implementation of the Observer Pattern in a data engineering context using Python:

##### Core Interfaces and Classes

``` py
from abc import ABC, abstractmethod

# Abstract Observer
class Observer(ABC):
    @abstractmethod
    def update(self, event_type, message):
        pass
```

``` py
# The ETL job is the Subject (Observable)
class ETLJob:
    def __init__(self):
        self.observers = []

    def attach(self, observer: Observer):
        self.observers.append(observer)

    def detach(self, observer: Observer):
        self.observers.remove(observer)

    def notify(self, event_type, message):
        for observer in self.observers:
            observer.update(event_type, message)

    def run(self):
        self.notify("START", "ETL job started.")
        try:
            self.extract()
            self.transform()
            self.load()
            self.notify("SUCCESS", "ETL job completed successfully.")
        except Exception as e:
            self.notify("FAILURE", f"ETL job failed: {e}")

    def extract(self):
        self.notify("EXTRACT", "Extracting data...")

    def transform(self):
        self.notify("TRANSFORM", "Transforming data...")

    def load(self):
        self.notify("LOAD", "Loading data...")
```

##### Observers: Logger, AlertService, and MetricsCollector

``` py
# Observer 1: Logger
class Logger(Observer):
    def update(self, event_type, message):
        print(f"[LOG - {event_type}] {message}")

# Observer 2: Alert system
class AlertService(Observer):
    def update(self, event_type, message):
        if event_type == "FAILURE":
            print(f"[ALERT] Sending alert: {message}")

# Observer 3: Metrics collector
class MetricsCollector(Observer):
    def update(self, event_type, message):
        print(f"[METRICS] Recording event: {event_type}")
```

##### Bringing It All Together

``` py
# Instantiate the ETL job
etl = ETLJob()

# Attach observers
etl.attach(Logger())
etl.attach(AlertService())
etl.attach(MetricsCollector())

# Run the job
etl.run()
```

### Implementing a Simple Event Handler in Python

- Here’s how you can build a basic event system to replace tightly coupled logic with event-based architecture:

``` py
class EventManager:
    def __init__(self):
        self.listeners = {}
        
    def subscribe(self, event_type, listener):
        if event_type not in self.listeners:
            self.listeners[event_type] = []
        self.listeners[event_type].append(listener)
        
    def unsubscribe(self, event_type, listener):
        if event_type in self.listeners and listener in self.listeners[event_type]:
            self.listeners[event_type].remove(listener)
            
    def dispatch(self, event_type, data=None):
        if event_type in self.listeners:
            for listener in self.listeners[event_type]:
                listener(data)
```

### Transforming to an Event-Based Approach

- To refactor tightly coupled code into a more modular event-driven system:

  - **Identify Events**: Determine which actions or stages in your system should emit events.
  - **Implement Event Manager**: Centralize event subscription and dispatch logic.
  - **Decouple Components**: Replace direct calls with event triggers and handlers.
  - **Create Listeners**: Implement functions or classes that respond to specific events.

#### Best Practices and Considerations

- While the observer pattern offers many benefits, keep in mind the following:
  - **Debugging Complexity**: Tracing event flows can be more difficult than tracing direct calls.
  - **Performance Overhead**: There’s a slight performance cost due to indirection.
  - **Memory Leaks**: Always ensure listeners are properly unsubscribed to avoid holding references.
  - **Avoid Overuse**: Not every interaction needs to be event-based. Use this pattern when it adds clarity or extensibility.

Reference

- <https://refactoring.guru/design-patterns/observer>
- <https://www.youtube.com/watch?v=-oLDJ2dbadA&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=12>
- <https://www.youtube.com/watch?v=_BpmfnqjgzQ>
