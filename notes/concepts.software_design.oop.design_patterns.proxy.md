---
id: qm7eum1o92thw8xizw8joeu
title: Proxy
desc: ''
updated: 1680423938941
created: 1680422475958
---

# Proxy
- Proxy is used in places where you want to add functionality to a class without
changing its interface. 
- The main class is called `Real Subject`. 
- A client should use the proxy or the real subject without any code change, so both must have the same interface. 
- Logging and controlling access to the real subject are some of the proxy pattern usages.

<br>

### What problems can the Proxy design pattern solve? 
- The access to an object should be controlled.
- Additional functionality should be provided when accessing an object.

<br>

### What solution does the Proxy design pattern describe?
- Define a separate Proxy object that 
  - can be used as substitute for another object (Subject) and
  - implements additional functionality to control the access to this subject.
  
<br>

``` python
import abc


class Subject(metaclass=abc.ABCMeta):
    """
    Define the common interface for RealSubject and Proxy so that a
    Proxy can be used anywhere a RealSubject is expected.
    """

    @abc.abstractmethod
    def request(self):
        pass


class Proxy(Subject):
    """
    Maintain a reference that lets the proxy access the real subject.
    Provide an interface identical to Subject's.
    """

    def __init__(self, real_subject):
        self._real_subject = real_subject

    def request(self):
        # ...
        self._real_subject.request()
        # ...


class RealSubject(Subject):
    """
    Define the real object that the proxy represents.
    """

    def request(self):
        pass


def main():
    real_subject = RealSubject()
    proxy = Proxy(real_subject)
    proxy.request()


if __name__ == "__main__":
    main()
```

Reference:
- https://sourcemaking.com/design_patterns/proxy
- https://en.wikipedia.org/wiki/Proxy_pattern
- https://www.youtube.com/watch?v=TS5i-uPXLs8&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=23