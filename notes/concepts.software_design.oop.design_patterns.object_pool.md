---
id: h4s34jt48h1avhupimfea7a
title: Object_pool
desc: ''
updated: 1680405882794
created: 1680403521466
---


### Object Pool 
- Most effective in situations where the cost of initializing a class instance is high, the rate of instantiation of a class is high, and the number of instantiations in use at any one time is low.
- Uses a pool of initialized objects that are ready to be used rather than creating a new object all the time. 
- The main idea of an Object Pool is that instead of creating instances of the class you can reuse them by getting them from the pool.

<br>

#### Use Case
- Let’s say for example, you need multiple firefox browser object in headless mode to be available for client request to process or some testing or scraping.
- Each time creating a new browser instance is time consuming task which will make client to wait.
- If you have one browser instance and manage with browser tab, it will become cumbersome to maintain and debug in case of any issue arises.
- Object Pool will help you to manage in that situation as it creates resource pool and provides to each client when it requests. Thus separating the process from one another without waiting or creating new instance on the spot.

<br>

### Example

``` python
"""
Offer a significant performance boost; it is most effective in
situations where the cost of initializing a class instance is high, the
rate of instantiation of a class is high, and the number of
instantiations in use at any one time is low.
"""


class ReusablePool:
    """
    Manage Reusable objects for use by Client objects.
    """

    def __init__(self, size):
        self._reusables = [Reusable() for _ in range(size)]

    def acquire(self):
        return self._reusables.pop()

    def release(self, reusable):
        self._reusables.append(reusable)


class Reusable:
    """
    Collaborate with other objects for a limited amount of time, then
    they are no longer needed for that collaboration.
    """

    pass


def main():
    reusable_pool = ReusablePool(10)
    reusable = reusable_pool.acquire()
    reusable_pool.release(reusable)


if __name__ == "__main__":
    main()
```

Reference:
- https://sourcemaking.com/design_patterns/object_pool
- https://www.youtube.com/watch?v=Rm4JP7JfsKY&t=407s