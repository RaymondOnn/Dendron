---
id: tq3b1513bapp283d7c9d61h
title: Factory
desc: ''
updated: 1680363425765
created: 1680355609675
---

### Factory
- A Factory is an object for creating other objects.


#### Example
- The code shows a way to localize words in two languages: English and
Greek. 
- `get_localizer` is the factory function that constructs a
localizer depending on the language chosen. 
- The localizer object will be an instance from a different class according to the language localized. 
- However, the main code does not have to worry about which localizer will be instantiated, since the method "localize" will be called in the same way independently of the language.

<br>

``` python
from typing import Dict
from typing import Protocol
from typing import Type


class Localizer(Protocol):
    def localize(self, msg: str) -> str:
        pass


class GreekLocalizer:
    """A simple localizer a la gettext"""

    def __init__(self) -> None:
        self.translations = {"dog": "σκύλος", "cat": "γάτα"}

    def localize(self, msg: str) -> str:
        """We'll punt if we don't have a translation"""
        return self.translations.get(msg, msg)


class EnglishLocalizer:
    """Simply echoes the message"""

    def localize(self, msg: str) -> str:
        return msg


def get_localizer(language: str = "English") -> Localizer:

    """Factory"""
    localizers: Dict[str, Type[Localizer]] = {
        "English": EnglishLocalizer,
        "Greek": GreekLocalizer,
    }

    return localizers[language]()


if __name__ == "__main__":
    # Create our localizers
    e, g = get_localizer(language="English"), get_localizer(language="Greek")
    
    # Localize some text
    for msg in "dog parrot cat bear".split():
    ...     print(e.localize(msg), g.localize(msg))
    >>> dog σκύλος
    >>> parrot parrot
    >>> cat γάτα
    >>> bear bear
```
<br>

Reference:
- https://www.youtube.com/watch?v=EdFq_JIThqM&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=3
- https://refactoring.guru/design-patterns/factory-method