---
id: ythuwloj4is2b32am8ounot
title: Decorator
desc: ''
updated: 1680357940124
created: 1680357616524
---

### Decorator
- The Decorator pattern is used to dynamically add a new feature to an
object without changing its implementation. 
- It differs from inheritance because the new feature is added only to that particular
object, not to the entire subclass.

#### Example
- This example shows a way to add formatting options (boldface and
italic) to a text by appending the corresponding tags (<b> and
<i>). 
- Also, we can see that decorators can be applied one after the other,
since the original text is passed to the bold wrapper, which in turn
is passed to the italic wrapper.


``` python
class TextTag:
    """Represents a base text tag"""

    def __init__(self, text: str) -> None:
        self._text = text

    def render(self) -> str:
        return self._text


class BoldWrapper(TextTag):
    """Wraps a tag in <b>"""

    def __init__(self, wrapped: TextTag) -> None:
        self._wrapped = wrapped

    def render(self) -> str:
        return f"<b>{self._wrapped.render()}</b>"


class ItalicWrapper(TextTag):
    """Wraps a tag in <i>"""

    def __init__(self, wrapped: TextTag) -> None:
        self._wrapped = wrapped

    def render(self) -> str:
        return f"<i>{self._wrapped.render()}</i>"


if __name__ == "__main__":
    simple_hello = TextTag("hello, world!")
    special_hello = ItalicWrapper(BoldWrapper(simple_hello))
    print("before:", simple_hello.render())
    >>> before: hello, world!
    
    print("after:", special_hello.render())
    >>> after: <i><b>hello, world!</b></i>
```

<br>

Reference:
- https://www.youtube.com/watch?v=v6tpISNjHf8&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=20
- https://refactoring.guru/design-patterns/decorator