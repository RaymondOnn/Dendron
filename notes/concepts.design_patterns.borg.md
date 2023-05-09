---
id: 5e34ljere46zooxc4z257pn
title: Borg
desc: ''
updated: 1680362307381
created: 1680361659226
---

### Borg
- The Borg pattern (also known as the Monostate pattern) is a way to
implement singleton behavior, but instead of having only one instance
of a class, there are multiple instances that share the same state. 
- In other words, the focus is on sharing state instead of sharing instance
identity.


<br>

#### Example
- To understand the implementation of this pattern in Python, it is important to know that, in Python, instance attributes are stored in a attribute dictionary called __dict__. 
- Usually, each instance will have its own dictionary, but the Borg pattern modifies this so that all instances have the same dictionary.
- In this example, the __shared_state attribute will be the dictionary shared between all instances, and this is ensured by assigning __shared_state to the __dict__ variable when initializing a new instance (i.e., in the __init__ method). 
- Other attributes are usually added to the instance's attribute dictionary, but, since the attribute dictionary itself is shared (which is __shared_state), all other
attributes will also be shared.

<br>


``` python
from typing import Dict


class Borg:
    _shared_state: Dict[str, str] = {}

    def __init__(self) -> None:
        self.__dict__ = self._shared_state


class YourBorg(Borg):
    def __init__(self, state: str = None) -> None:
        super().__init__()
        if state:
            self.state = state
        else:
            # initiate the first instance with default state
            if not hasattr(self, "state"):
                self.state = "Init"

    def __str__(self) -> str:
        return self.state


if __name__ == "__main__":
    rm1 = YourBorg()
    rm2 = YourBorg()
    rm1.state = 'Idle'
    rm2.state = 'Running'
    print('rm1: {0}'.format(rm1))
    >>> rm1: Running
    
    print('rm2: {0}'.format(rm2))
    >>> rm2: Running
    
    # When the `state` attribute is modified from instance `rm2`,
    # the value of `state` in instance `rm1` also changes
    rm2.state = 'Zombie'
    print('rm1: {0}'.format(rm1))
    >>> rm1: Zombie
    
    print('rm2: {0}'.format(rm2))
    >>> rm2: Zombie
    
    # Even though `rm1` and `rm2` share attributes, the instances are not the same
    rm1 is rm2
    >>> False
    
    # New instances also get the same shared state
    rm3 = YourBorg()
    print('rm1: {0}'.format(rm1))
    >>> rm1: Zombie
    
    print('rm2: {0}'.format(rm2))
    >>> rm2: Zombie
    
    print('rm3: {0}'.format(rm3))
    >>> rm3: Zombie
    
    # A new instance can explicitly change the state during creation
    rm4 = YourBorg('Running')
    print('rm4: {0}'.format(rm4))
    >>> rm4: Running
    
    # Existing instances reflect that change as well
    print('rm3: {0}'.format(rm3))
    rm3: Running

```