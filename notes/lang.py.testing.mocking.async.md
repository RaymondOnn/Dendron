---
id: 1my0qlf3mv4namejz05tzga
title: Async
desc: ''
updated: 1713928030648
created: 1713928015630
---


### Mocking asynchronous iterators
- Since Python 3.8, AsyncMock and MagicMock have support to mock Asynchronous Iterators through `__aiter__`.
- The return_value attribute of `__aiter__` can be used to set the return values to be used for iteration.

    ``` py
    mock = MagicMock() # AsyncMock also works here
    mock.__aiter__.return_value = [1, 2, 3]
    async def main():
        return [i async for i in mock]

    asyncio.run(main())
    [1, 2, 3]
    ```
### Mocking asynchronous context manager
- Since Python 3.8, AsyncMock and MagicMock have support to mock Asynchronous Context Managers through `__aenter__` and `__aexit__`. 
- By default, `__aenter__` and `__aexit__` are AsyncMock instances that return an async function.

    ``` py
    class AsyncContextManager:

        async def __aenter__(self):
            return self
        async def __aexit__(self, exc_type, exc, tb):
            pass

    mock_instance = MagicMock(AsyncContextManager()) # AsyncMock also works here
    async def main():
    async with mock_instance as result:
    pass

    asyncio.run(main())
    mock_instance.__aenter__.assert_awaited_once()
    mock_instance.__aexit__.assert_awaited_once()
    ```