---
id: ivdo3iz0ekwcnhkm7c2o2kz
title: lifecycle
desc: ''
updated: 1723484954166
created: 1723482209023
---


### `TaskGroup`

- `TaskGroup` is created using an asynchronous context manager
- concurrent tasks can be added to the group by the method `create_task`

``` py
async def main():
    async with asyncio.TaskGroup() as tg:
        tg.create_task(some_coro(1))
        tg.create_task(other_coro(2))
    print("Both tasks have completed now.")
```

- When the context manager exits, it waits for all tasks in the group to complete.
- While waiting, we can still add new tasks to TaskGroup.
- If a task in the group throws an exception other than `asyncio.CancelledError` while waiting, all other tasks in the group will be canceled.
- All exceptions were thrown except for `asyncio.CanceledError` will be combined and thrown in the ExceptionGroup.

### `timeout`

- `asyncio.timeout`: limits the execution time of concurrent code in a context.
- It is also created using the asynchronous context manager.

#### Problem

- Suppose we need to set a timeout to a single function call, it is sufficient to call `asyncio.wait_for`

    ``` py
    async def main():
        await asyncio.wait_for(some_coro(delay=2), timeout=1)
    ```

- Suppose we now have two concurrent tasks and want them to run to completion in 8 seconds.

    ``` py
    # Assign an average timeout of 4 seconds to each task.
    async def main():
        await asyncio.wait_for(some_coro(delay=5), timeout=4)
        await asyncio.wait_for(other_coro(delay=2), timeout=4)
    ```

- Although we set an average timeout for each concurrent method, such a setting may cause uncontrollable situations since each call to the IO-bound task is not guaranteed to return simultaneously, resulting in a `TimeoutError`.

#### Solution

- we use the `asyncio.timeout` block to ensure that we set an overall timeout for all concurrent tasks:

``` py
async def main():
    async with asyncio.timeout(delay=6):
        async with asyncio.TaskGroup() as tg:
            tg.create_task(some_coro(delay=5))
            tg.create_task(other_coro(delay=2))
    print("All tasks have completed in time.")
```

### What is Structured Concurrency
- `TaskGroup` and `asyncio.timeout` uses the `async with` feature. Just like `with` struct block can manage the life cycle of resources uniformly like this:

But calling concurrent tasks inside with block does not work because the concurrent task will continue executing in the background while the with block has already exited, which will lead to improper closure of the resource:

Therefore, we introduced the async with feature here. As with, async with and TaskGroup Is used to manage the life cycle of concurrent code uniformly, thus making the code clear and saving development time. We call this feature our main character today: Structured Concurrency.

Why Structured Concurrency is so important

1. History of concurrent programming
Before the advent of concurrent programming, we executed our code serially. Code would perform for_loop loops, if_else conditional branches, and function calls sequentially, depending on the order in the call stack.

Image by author
However, as the speed of code execution became more and more demanding in terms of computational efficiency and as computer hardware developed significantly, parallel programming (CPU bound) and concurrent programming (IO bound) gradually emerged.

Before coroutine emerged, Python programmers used threading to implement concurrent programming. But Python’s threads have a problem, that is, GIL (Global Interpreter Lock), the existence of GIL makes the thread-based Concurrency unable to achieve the desired performance.

So asyncio coroutine emerged. Without GIL and inter-thread switching, concurrent execution is much more efficient. If threads are time-slice-based task switching controlled by the CPU, then coroutine is the creation and switching of subtasks back into the hands of the programmer himself. While programmers enjoy convenience, they also encounter a new set of problems.

2. Problems with the Concurrent Programming Model
As detailed in this article, concurrent programming raises several issues regarding control flow.

Concurrent programming is opening up multiple branch processes in our main thread. These branch tasks silently perform network requests, file accesses, database queries, and other duties in the background.

Concurrent programming will change the flow of our code from this to this:

Image by author
According to the “low coupling, high cohesion” rule of programming, we all want to join all the background tasks in a module together after execution like this:

Image by author
But the fact is that since multiple members develop our application or call numerous third-party components, we need to know which tasks are still executing in the background and which tasks are finished. It’s more likely that one background task will branch into several other branch tasks.

Ultimately, these branching tasks need to be found by the caller and wait for their execution to complete, so it becomes like this:

Image by author
Although this is not Marvel’s multiverse, the situation is now just like the multiverse, bringing absolute chaos to our natural world.

Some readers say that asyncio.gather could be responsible for joining all the background tasks. But asyncio.gather it has its problems:

It cannot centrally manage backend tasks in a unified way. Often creating backend tasks in one place and calling asyncio.gather in another.
The argument aws received by asyncio.gather is a fixed list, which means that we have set the number of background tasks when asyncio.gather is called, and they cannot be added randomly on the way to waiting.
When a task is waiting in asyncio.gather throws an exception, it cannot cancel other tasks that are executing, which may cause some tasks to run indefinitely in the background and the program to die falsely.
Therefore, the Structured Concurrency feature introduced in Python 3.11 is an excellent solution to our concurrency problems. It allows the related asynchronous code to all finish executing in the same place, and at the same time, it will enable tg instances to be passed as arguments to background tasks, so that new background tasks created in the background tasks will not jump out of the current life cycle management of the asynchronous context.

Thus, Structured Concurrency is a revolutionary improvement to Python asyncio.

