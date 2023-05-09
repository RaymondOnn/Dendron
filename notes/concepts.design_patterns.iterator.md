---
id: hv9k4fxqkgom35n7j231wvu
title: Iterator
desc: ''
updated: 1680406224561
created: 1680346381254
---

### Iterator

``` python
def count_to(count: int):
    """Counts by word numbers, up to a maximum of five"""
    numbers = ["one", "two", "three", "four", "five"]
    yield from numbers[:count]


# Test the generator
def count_to_two() -> None:
    return count_to(2)


def count_to_five() -> None:
    return count_to(5)


if __name__ == "__main__":
    # Counting to two...
    for number in count_to_two():
    ...     print(number)
    >>> one
    >>> two
   
    # Counting to five...
    for number in count_to_five():
    ...     print(number)
    >>> one
    >>> two
    >>> three
    >>> four
    >>> five
```

References:
- https://www.youtube.com/watch?v=QCWJWfuAfJc&list=PLlsmxlJgn1HJpa28yHzkBmUY-Ty71ZUGc&index=15
- https://sourcemaking.com/design_patterns/iterator