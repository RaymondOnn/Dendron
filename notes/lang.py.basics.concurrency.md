---
id: om9xqxufmnsms0abvorm9kl
title: concurrency
desc: ''
updated: 1723393775418
created: 1693297096783
---
<https://www.youtube.com/watch?v=X7vBbelRXn0&ab_channel=mCoding>

| Concurrency Type | Features | Use Criteria | Metaphor |
|--------------|-----------|------------|------------|
Multiprocessing | Multiple processes, high CPU utilization. | CPU-bound | We have ten kitchens, ten chefs, ten dishes to cook. |
Threading | Single process, multiple threads, pre-emptive multitasking, OS decides task switching. | Fast but I/O-bound | We have one kitchen, ten chefs, ten dishes to cook. Only one chef can cook at a time |
AsyncIO | Single process, single thread, cooperative multitasking, tasks cooperatively decide switching. | Slow and I/O-bound | We have one kitchen, one chef, ten dishes to cook |

### What is Concurrency

- Concurrency is a concept that refers to how a program, or algorithm is structured
- It is not how a program is executed as there are different ways of executing concurrent code
- An algorithm is concurrent when it is **structured** into several sub parts that can be executed **out of order** (or partially order), without affecting the **outcome**
  - Code sections are not necessarily executed in a specific order
  - But the final result remains the same
  - for e.g averaging a list of N numbers
    - option 1: get the sum and count of all numbers and then calculate the average
    - option 2:
      - split the list of numbers into parts
      - get the sum and count of numbers for each part
      - calculate the average:

        ``` latex
        average = (sum1 + sum2 + ... + sumN) / (count1 + count2 + ... + countN) 
        ```

### How are concurrent fragments executed?

#### Parallel Execution

- Fragments are executed at the same time on separate cpu cores
- Hence requires multiple CPUs (or cores)
- code executor must be able to take advantage of multiple CPUs/cores

#### Time-Sliced Execution

- Execution is alternated between fragments after certain amount of time on the same CPU
- Looks like parallel execution but actually is not
- Only a single CPU/core is needed
- Uses context switching, also called interleaving
  - Every time execution is paused, state must be saved somewhere
  - Every time execution is resummed, saved state must be loaded
  - Each context switch has a performance cost

### Processes

- Think of a process as an application instance executing in your OS e.g. Excel, Web Browser, Python app
- Process run concurrently
  - May run in paraallel on a machine with multiple CPUs/cores
  - or using interleaving/time-slicing (even on a multi CPU machine)
- Each process runs independently
  - memory is not shared between process (as well as some other resources)
- Possible states
  - running: Has access to CPU and is currently executing code
  - ready: could run, if it had access to a CPU
  - blocked: waiting for something to happen (e.g. I/O operation, such as receiving data from an API)

### Threads

- Each process is executed using one or more threads ( in modern operating systems)
- Always starts with at least one thread, often called the main thread
  - Shared access to "global" data
- Concurrent code can be executed using multiple threads i.e. Multi-threaded process
  - Otherwise, called a single-threaded process
- Threads have access to shared resources in the process (global data, open files etc)
- Threads can also have their own "private" resources (i.e. local variables, sometimes called thread-local data)
- May run in parallel on machine with multiple CPUs/cores and/or use time-slicing/interleaving (even on a multi-CPU machine)

### Multitasking

- General term used to denote that multiple things are running concurrently
  - includes:
    - multiple processes
    - multiple threads
    - or any other way of running code fragments **concurrently**, not parallel

#### Scheduling

- OS has a piece of software called the scheduler
- The scheduler decides when to run, pause and restart threads
  - NOTE: Multiple running processes results in multiple threads, even if each process is single-threaded
- When switching between thread, **context switching takes place, which incur a performance penalty**
  - When a thread is paused, its current state must be saved (in memory)
  - When a thread is resumed, its original saved state must be restored
- We say the scheduler preempts the thread, hence the term **preemptive multitasking**

#### The Python Global Interpreter Lock

- Also called the GIL for short
- Makes writing the CPython interpreter easier and actually speeds up single threaded processes which is how most python apps are written
- However, only allows for interleaved/time-sliced running of multiple threads
  - Even when we create multiple threads, only one thread is allowed to run at a time
  - No parallism and does not take advantage of multiple CPUs

#### Another approach to multitasking

- The scheduler implements preemptive multithreading
  - Here we don't have control over when one thread is stopped and another started
  - Can be difficult when using shared resources
    - A lot of care is needed to ensure things run as expected even if task is interrupted preemptively
- Alternatively, there is another approach called cooperative multitasking
  - Here we write code where we explicitly say when or where a task can be interrupted to allow another task to run concurrently
  - This can be really useful in cases where a code fragment is waiting on an external resource to complete and return something
    - waiting for some I/O operation to complete
      - database response or response from web API
      - a file to be opened and read
      - another process to return something

### Workloads

- Characterize the type of work done by a chunk of code
  - Usually based on what kind of work the process is dealing with majority of the time
- CPU workload:
  - spends a lot of time running computations
  - would benefit from a more powerful CPU/core
- I/O workload
  - spends a lot of time waiting for I/O to complete
  - would not benefit from a more powerful CPU
- Most workloads are mixed of the two, but have a dominant bottleneck
  - CPU workload -> CPU Bound
  - I/O workload -> I/O Bound

#### CPU Bound workloads with Threading

- Assumes the workload can be written into concurrent fragments
- Running fragments using multiple threads would seem like a good solution to increase performance
- However, due to the GIL, threads will run interleaved, not in parallel across multiple CPUs
- So no paralle execution and we incur the additional cost of context switching

#### CPU Bound workloads with Multiprocessing

- One way to spread CPU bound loads across multiple CPUs is to start multiple parallel processes
- With multiprocessing, process state is not shared since they are independent
  - Hence communicating between main app and other process is more difficult/costly than with threading
- Main application can spawn multiple process i.e. data marshalling
  - can pass data to a spawned process
  - can get results back from a process
  - Not scalable since limited to the CPUs of a single machine
- In modern computing, and with ever larger data sets, scaling limited to a single machine is often not sufficient
- While multiprocessing can be used to take full advantage of a single machine, to get that level of performance will likely need to write more complex code to scale across multiple machines

#### I/O Bound workloads with Threading

- CPython is inherently single threaded
- Running code concurrently makes sense, even if single-threaded, since most of the time is spent waiting for an I/O operation to return something
- Switching to another thread while one is waiting for I/O means other code fragments can be executing code that they would otherwise have been blocked from running until the I/O operation completed

##### Multithreading Dangers

- Multithreading concurrent I/O bound fragments should improve performance
- However, multithreaded code is difficult to write
  - With preemptive multitasking, we don;t know exactly when a thread will be interrupted
  - Will have to be careful with shared state as it is easy to run into problems, especially when the order of operations matters
  - the same code sometimes return the correct result, and sometimes return the wrong result
  - Extra work needed to guard agianst such issues (locks, queues etc.)
  - Also difficult to debug

### Asynchronous programming

- Cooperative Multitasking offers a simpler/safer alternative for writing concurrent code for I/O bound workloads
- A form of cooperaitive multitasking is asynchronous programming
- Can significantly improve performance for I/O bound workloads
- Uses `asyncio` module with keywords such as `async`, `await`, `yield`
- Here we have to explicity let Python know how the different fragments can interrupt each other and work together
  - This adds some complexity to the code
    - blocking code: can be interrupted to run other fragments
    - non-blocking code: has to run sequentially
  - Generally simpler overall than multithreading, easier to debug but require 3rd party I/O libraries to also be aync enabled

#### The Asyncio Event Loop
- Basic idea is we "register" concurrent code fragments that run code, and intermittently indicate that a particular line of code would be a good itme to interrupt the function
  - these calls to async enabled functions are called task
  - we end up with a collection of these tasks that need to be executed
- Python creates an event loop - single threaded
  - It runs one task at a time
  - The tasks indicates that it is ready to be switched (or completes), usually because it is waiting for I/O respoinse
  - The event loop gets control back and starts running another tasks, until that task indicates it is ready to be switched (or completes)
  - This continues until all tasks are completed
- When a task runs, it is executing code, and at some point should yield control back to the event loop
- Functions or tasks that run and do not yield control back before they have completed are called blocking
- It is usual for tasks to contain blocking code (since we do have some computations to do)
- Should be ok as long as we yield control once in a while, especially when we are waiting for I/O
- To get the most benefit from async-based concurrency, we need to be able to yield control ( not block) when performing I/O operations