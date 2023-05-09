---
id: u4rg3x330z19rrk4ylth2ms
title: Mediator
desc: ''
updated: 1680349111034
created: 1680346837648
---


### Mediator
- Objects in a system communicate through a Mediator instead of directly with each other.
- This reduces the dependencies between communicating objects, thereby reducing coupling.
- Reduce chaotic dependencies between objects by restricts direct communications between the objects and forces them to collaborate only via a mediator object.

<br>

``` python
from __future__ import annotations


class ChatRoom:
    """Mediator class"""

    def display_message(self, user: User, message: str) -> None:
        print(f"[{user} says]: {message}")


class User:
    """A class whose instances want to interact with each other"""

    def __init__(self, name: str) -> None:
        self.name = name
        self.chat_room = ChatRoom()

    def say(self, message: str) -> None:
        self.chat_room.display_message(self, message)

    def __str__(self) -> str:
        return self.name


def main():
    """
    >>> molly = User('Molly')
    >>> mark = User('Mark')
    >>> ethan = User('Ethan')
    >>> molly.say("Hi Team! Meeting at 3 PM today.")
    [Molly says]: Hi Team! Meeting at 3 PM today.
    >>> mark.say("Roger that!")
    [Mark says]: Roger that!
    >>> ethan.say("Alright.")
    [Ethan says]: Alright.
    """


if __name__ == "__main__":
```



Reference:
- https://www.youtube.com/watch?v=35D5cBosD4c&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=10