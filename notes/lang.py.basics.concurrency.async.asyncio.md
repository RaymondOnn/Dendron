---
id: x9lfxul5lw8qmefkb8ide76
title: asyncio
desc: ''
updated: 1728231909677
created: 1723465566326
---


### How Async Works on the Low Level
>
>“Asynchronous I/O is a form of input/output processing that permits other processing to continue before the transmission has finished.” — Wikipedia

#### Multiprocessing

- A simple approach to asynchronous IO would be to take all the blocking operations and place them in another process.
- Then, the main process would communicate with the IO process using sockets.
- The advantage of this method is relative simplicity for OS developers — once you implement multitasking, the system will support asynchronous programming.
- On the downside, this may result in wasted resources and complicated programming for the software itself.

#### Polling

- This is a very common implementation of asynchronous IO.
- In this case, the OS is responsible for managing the IO operations.
  - The program will instruct the system to perform the IO operation specified, and the system will do so in the background.
  - The program will periodically ask (poll) the system on the status of the operation.
  - Once finished, the poll will return the data.

- The advantage of this method is it is easy to use in the programs and pretty intuitive.
- However, this can result in wasted resources, as the program would spend CPU cycles asking the system about the status of the operations (potentially millions of times!).

#### Interrupts/callbacks

- This is the most performant and elegant approach to asynchronous IO.
- The program will ask the system to perform an IO operation and continue working.
  - Once the IO operation is done, the system will notify the program by a signal, interrupt, or callback.
  - This will cause the program to stop whatever it is doing and deal with the incoming data.
- One important implication of this approach is that the program can be interrupted at an unpleasant time — for example, in the middle of a blocking operation or a long computation.
- Several methods are used to mitigate this, but they are out of the scope of this article.

### How Async Works in Python

#### Generator

- Python Generators are special functions that can suspend their execution and resume it later.
- They are often used as iterator functions; the most common one is range.
- Generators use the `yield` keyword to return a value without completely exiting the function.
- It can be continued to get more values.

    ``` py
    # A simple example of a generator that returns numbers from 1 to 5:

    def get_numbers():
        yield 1
        yield 2
        yield 3
        yield 4
        yield 5

    for i in get_numbers():
        print(i)

    >> 1
    >> 2
    >> 3
    >> 4
    >> 5
    ```

- `yield from`: Indicate that the next value should be yielded from another generator. This allowed users to combine generators

    ``` py
    # Consider this example, which does the same thing as the first but with extra steps:

    def get_all_numbers():
        yield 1
        yield from get_some_numbers()
        yield 5

    def get_some_numbers():
        yield 2
        yield 3
        yield 4

    for i in get_all_numbers():
        print(i)

    >> 1
    >> 2
    >> 3
    >> 4
    >> 5
    ```

#### Coroutine

- Coroutines are the basic building blocks of asynchronous workflows in Python
- A coroutine is a function that can be paused (suspended) and resumed later.

>“Coroutines are a more generalized form of subroutines. Subroutines are entered at one point and exited at another point. Coroutines can be entered, exited, and resumed at many different points. They can be implemented with the async def statement.” — Python docs

- Coroutines and generators are very similar, but there are a few key differences.
  - Coroutines are defined using the `async def` keyword and can use the `await` syntax inside.
  - The `await` keyword is very similar to `yield from`
  - it turns the control over to another function and tells the caller that it now has to wait for it to complete.

    ``` py
    async def my_coroutine():
        print('coroutine started')
        # pauses our coroutine for 1 second
        await asyncio.sleep(1)
        print("coroutine ended")
    ```

- when a coroutine is called, it returns a coroutine object.
- the coroutine object is passed to the event loop.
- the event loop then schedules the coroutine to be executed when resources become available


#### Task

- If coroutines are special functions, tasks are entities that run such functions.
- A task accepts a coroutine and provides an interface to run and oversee the coroutine i.e. check its completion status, get the resulting data, or see the error message.

>“Tasks are used to run coroutines in event loops. If a coroutine awaits on a Future, the Task suspends the execution of the coroutine and waits for the completion of the Future. When the Future is done, the execution of the wrapped coroutine resumes.” — Python docs

#### Event loop

- The event loop is the engine behind asynchronous applications, and it is responsible for running Tasks.
- The event loop holds in memory all Tasks currently running in the program.
- It will walk them one by one and see if a Task is waiting on something or ready to be executed.
- It will execute the pending code, and the Task will either end up waiting for something else or exit.
- The event loop will run endlessly until the whole program terminates.

#### Creating and Running Coroutines

- To create functions in Python, we write `def functionname(arguments...)`.
- Coroutines are special kinds of functions and thus have special kind of definitions: `async def:`

    ``` py
    async def my_coroutine():
        ...
    ```

- In the function body, we can call other subroutines and await for them:

    ``` py
    import asyncio

    async def my_coroutine():
        await asyncio.sleep(1)
    ```

  - `asyncio.sleep()` is also a coroutine. This is why we can `await` it.
  - `await` means that we give control to another coroutine and must wait until it is finished.

- `asyncio.run()` for running coroutines

    ``` py
    import asyncio

    async def my_coroutine():
        print("before sleeping")
        await asyncio.sleep(1)
        print("after sleeping")

    asyncio.run(my_coroutine())

    >> before sleeping
    >> after sleeping
    ```

  - `asyncio.run()` accepts a coroutine and runs it in the main event loop.
  - Note that the call to run is synchronous and blocking: it starts an event loop, which will only run coroutines.

        ``` py
        import asyncio

        async def my_coroutine():
            print("before sleeping")
            await asyncio.sleep(1)
            print("after sleeping")

        asyncio.run(my_coroutine())
        print("after event loop")

        >> before sleeping
        >> after sleeping
        >> after event loop
        ```

#### Running Blocking Operations Asynchronously
- Functions or tasks that run and do not yield control back before they have completed are called blocking
    - Here, `requests.get` is written as a plain old function and do not have the ability to `await`.

    ``` py
    # Calling an API called CatFacts, which is supposed to give us a random cat fact:
    
    import asyncio
    import requests

    ENDPOINT = "<https://catfact.ninja/fact>"

    async def call_api():
        result = await requests.get(ENDPOINT)
        print(result.json())

    asyncio.run(call_api())

    # Output:
    Traceback ...
    ...
    File "api-request-bad-example.py", line 8, in call_api
        result = await requests.get(ENDPOINT)
    TypeError: object Response can't be used in 'await' expression
    ```

- `run_in_executor()`: Takes a runnable and run it in a separate thread, controlled by the main event loop, allowing the program to run without blocks and interface with asyncio and `await` it.

    ``` py
    # Below is the API call example rewritten using run_in_executor:

    import asyncio
    import requests

    ENDPOINT = "<https://catfact.ninja/fact>"

    async def call_api():
        loop = asyncio.get_event_loop() # get access to the event loop
        # Execute our blocking code in a separate thread
        # 1st arg set to None to use default executor
        result = await loop.run_in_executor(None, lambda: requests.get(ENDPOINT))
        print(result.json())

    asyncio.run(call_api())
    ```

- Refactoring the code to simplify it.

    ``` py
    # aiocat.py
    import asyncio
    import requests

    ENDPOINT = "<https://catfact.ninja/fact>"

    async def get_cat_fact():
        loop = asyncio.get_event_loop()
        result = await loop.run_in_executor(None, lambda: requests.get(ENDPOINT))
        return result.json().get('fact')

    async def main():
        print(await get_cat_fact())

    asyncio.run(main())
    ```

#### Run Multiple Coroutines Together

- `asyncio.gather()`: takes all the awaitables passed and `await` them all at once.
- They will start simultaneously, and the function will return when they finish.

    ``` py
    # fetching five cat facts from the cat fact service
    
    import asyncio
    import time

    from aiocat import get_cat_fact

    async def main():
        facts = await asyncio.gather(
            get_cat_fact(),
            get_cat_fact(),
            get_cat_fact(),
            get_cat_fact(),
            get_cat_fact()
        )
        print(*facts, sep='\\n')

    start = time.perf_counter()
    asyncio.run(main())
    print(f'Completed in {time.perf_counter() - start}s')
    ```

#### Tasks in asyncio

- A Task is a class within asyncio that gives us powerful ways to schedule, control, and observe coroutines.
- A Task is simply a wrapper around a coroutine that allows us to inspect it and perform certain actions (cancel, for example).
- To create a Task, you can use the function `asyncio.create_task`. You will need to pass a coroutine instance to it.

    ``` py
    import asyncio

    from aiocat import get_cat_fact

    async def main():
        get_fact_task = asyncio.create_task(get_cat_fact())
        result = await get_fact_task
        print(result)

    asyncio.run(main())
    ```

##### Naming tasks

- `Task.set_name()`: Assign names to tasks to identify them and use them for logging purposes.
- `Task.get_name()`: Get the name of the task.

``` py
import asyncio

from aiocat import get_cat_fact

async def main():
    get_fact_task = asyncio.create_task(get_cat_fact())
    get_fact_task.set_name('Task to get a random fact about cats')
    result = await get_fact_task
    print(f'Finished running task: {get_fact_task.get_name()}')
    print(result)

asyncio.run(main())

# Output:
Finished running task: Task to get a random fact about cats
A cat only has the ability to move their jaw up and down, not side
to side like a human can.
```

##### Check the status of the task

- After you have created the task, it is scheduled to run in the event loop.
- `Task.done()`: Check if task has completed
- `Task.cancel()`: Cancel the task
- `Task.cancelled()`: Check if task was cancelled

``` py
import asyncio

from aiocat import get_cat_fact

async def main():
    task1 = asyncio.create_task(get_cat_fact())
    task2 = asyncio.create_task(get_cat_fact())
    print(f'Task 1: done: {task1.done()}, cancelled: {task1.cancelled()}')
    print(f'Task 2: done: {task2.done()}, cancelled: {task2.cancelled()}')

    task1.cancel() # cancel first task
    await task2 # wait until second task is done
    print(f'Task 1: done: {task1.done()}, cancelled: {task1.cancelled()}')
    print(f'Task 2: done: {task2.done()}, cancelled: {task2.cancelled()}')

asyncio.run(main())

# Output:
Task 1: done: False, cancelled: False
Task 2: done: False, cancelled: False
Task 1: done: True, cancelled: True
Task 2: done: True, cancelled: False
```

##### Scheduling Tasks

The way tasks are executed is slightly different from how coroutines are executed directly.

- If you write results = await get_cat_fact(), the coroutine is created, and execution is suspended until we get a result.
- When we write asyncio.create_task(get_cat_fact()), the routine is wrapped up into a task but does not execute immediately. Instead, it is scheduled to run in the event loop. Basically, asyncio tells you this task will run at some point but does not specify when.

Practically, to get to run this Task, the event loop must have some time to spare. If you are waiting for something (some other coroutine), it may execute pending tasks in the background. You can also await the Task directly (like in the example above), and this will be the cue for asyncio to run it immediately.

To illustrate how tasks are scheduled and run, let’s write one more example. Firstly, we will modify our get_cat_fact() function. As is, API requests happen too fast, and with no errors, so I will add some random delays and errors to illustrate the features of tasks:

async def real_get_cat_fact():
    await asyncio.sleep(random.random())
    if (random.random() > .3):
        return get_cat_fact()
    else:
        raise Exception('API Error!')
We will sleep up to three seconds, and any probability lower than 30% will throw an error. Now, let’s move on to our next example. In this one, we will create a couple of Tasks and try sleeping until all of them are complete while watching their progress:

import asyncio

from aiocat import real_get_cat_fact

def check_task_status(task: asyncio.Task):
    # check if task is done and print results
    if task.done():
        if task.exception() is not None:
            print(f'Task #{task.get_name()} failed')
            return True
        print(f'Task #{task.get_name()} complete, result - {task.result()}')
        return True
    return False
    # else task is in progress

async def main():
    # create array of tasks with numbered names
    tasks = [asyncio.create_task(real_get_cat_fact(), name=str(i+1)) for i in range(5)]

    while tasks:
        # check status of tasks and remove those that are finished
        tasks = [task for task in tasks if not check_task_status(task)]
        await asyncio.sleep(0.1)

asyncio.run(main())
Firstly, take a look at the check_task_status function. It uses the .done() and .result() functions that we have seen, and a new one — .exception(), which returns an exception if your task throws one. The check_task_status function checks if a task is finished executing, print its results, and return true. If it is still in progress, it returns false.

In the main function, we first create a list of tasks from 1 to 5. Then, we go in a loop until we run out of tasks in the tasks array. We check the status of each task and remove the finished ones. Then we sleep for 100ms.

If you run this, you will get an output similar (but different) to this:

Task #2 failed
Task #1 complete, result - The leopard is the most widespread of all big cats.
Task #3 complete, result - Blue-eyed, pure white cats are frequently deaf.
Task #4 complete, result - When your cats rubs up against you, she is actually marking you as 'hers' with her scent. If your cat pushes his face against your head, it is a sign of acceptance and affection.
Task #5 complete, result - The Ancient Egyptian word for cat was mau, which means "to see".
Remember when I said the event loop will execute our background tasks in its free time? In this example, the execution happens while we await asyncio.sleep, or when we instruct the interpreter to wait 100 ms. This is another way of using tasks — scheduling background operations to run when our code is not doing anything else.

#### Task callbacks

The last feature of tasks that we will look at is callback functions. You can set up functions that will be called automatically when a task finishes. Let’s rewrite the above example using such functions. To add a callback to the task, you can use the function add_done_callback. You pass in the callback, and it will be called with the task object once the task is done, failed, or cancelled.

``` py
import asyncio

from aiocat import real_get_cat_fact

def check_task_status(task: asyncio.Task):
    if task.done():
        if task.exception() is not None:
            print(f'Task #{task.get_name()} failed')
            return
        print(f'Task #{task.get_name()} complete, result - {task.result()}')

async def main():
    tasks = []
    for i in range(5):
        task = asyncio.create_task(real_get_cat_fact())
        task.set_name(str(i + 1))
        task.add_done_callback(check_task_status)
        tasks.append(task)
    await asyncio.gather(*tasks, return_exceptions=True)

asyncio.run(main())
```

In this example, we create a list of tasks using a for loop and use the check_task_status function as a callback. It will print the result of the task or an error message. Lastly, use asyncio.gather to wait until all tasks are complete.

#### Iterators in asyncio

- Regular iterator vs Asynchronous iterator: Need to await the result of the asynchronous iterator.
  - Traversing an iterator,
    - regular: use the `for` operator.
    - asynchronous: use `async for`.
  - Getting the next value in the iterator,
    - regular: use `next()` function
    - asynchronous: use `await anext()`

- To implement a custom asynchronous iterator, you need a class that implements `__aiter()__` and `__anext()__` functions.
  - `__aiter()__` should return an instance of the iterator
  - `__anext()__` should return an awaitable result of the iterator.

``` py
# An iterator CatFactIterator that will produce five cat facts asynchronously:

import asyncio
from aiocat import get_cat_fact

class CatFactIterator:
    # how many facts were produced
    count: int

    def __init__(self):
        # initialize counter
        self.count = 0

    def __aiter__(self):
        """ Return instance of class"""
        return self

    def __anext__(self):
        """ Gets next cat fact"""
        if self.count == 5:
            # indicate job complete; stop after 5 facts
            raise StopAsyncIteration("Ran out of cat facts!")

        self.count += 1
        return get_cat_fact()

async def main():
    # create instance of iterator
    facts = CatFactIterator()
    # iterate asynchronously over iterator
    async for fact in facts:
        print(fact)

asyncio.run(main())
```

#### Context Managers in asyncio

- Context managers are used to allocate and release resources used in your program.

    ``` py
    # One popular context manager is the open function:
    with open('file', 'r') as f:
        # file opens
        f.write('something')
        # file closes automatically
    ```

- Context managers are typically used when you work with resources that you have to allocate (open, connect) and release (close, disconnect) when you are done.
- To create an asynchronous context manager, you need to define a class that implements `__aenter__` and `__aexit__` functions.
  - `aenter` is called when you enter a context manager
  - `aexit` is called when you exit the context manager.
  - To use it, you need to write `async with` instead of regular `with`.

##### Making HTTP Requests in asyncio the Right Way

So far, we have explored all the main topics of asyncio, which should be enough to start working on most of the everyday tasks of a Python developer. As a bonus, I would like to introduce you to the library aiohttp, an asynchronous HTTP client.

If you recall our earlier example, when we used requests to call an API, it was hacky. We had to use loop.run_in_executor because requests does not support asynchronous programming natively.

Instead, I highly recommend using a native asynchronous library, such as aiohttp when making HTTP calls. It is faster, safer, and easier to use.

To install aiohttp, run the following command:

pip install aiohttp
Now, let’s call the cat fact API we have been using. The code below does exactly that:
    ``` py
    import asyncio
    import aiohttp

    async def main():
        async with aiohttp.ClientSession() as session:
            async with session.get('<https://catfact.ninja/fact>') as response:
                data = await response.json()
                print(data.get('fact'))

    asyncio.run(main())
    ```
    - In the main function, we first use the `ClientSession async` context manager to create a session. 
    - Then we use another context manager `session.get` to perform an HTTP GET request. 
    - Then we await its JSON, and lastly, print the cat fact.
