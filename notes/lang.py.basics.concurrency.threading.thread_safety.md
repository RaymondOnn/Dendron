---
id: twawgxeq3jk325w1k22sowd
title: thread_safety
desc: ''
updated: 1730393036706
created: 1730382241961
---

<https://realpython.com/python-thread-lock/>

### Recap

- Concurrency: The ability of a system to handle multiple tasks by allowing their execution to overlap in time but not necessarily happen simultaneously.
- Parallelism: The simultaneous execution of multiple tasks that run at the same time to leverage multiple processing units, typically multiple CPU cores.

#### Threading

- Python’s threading is a concurrency framework that allows you to spin up multiple threads that run concurrently, each executing pieces of code.
- This improves the efficiency and responsiveness of your application. When running multiple threads, the Python interpreter switches between them, handing the control of execution over to each thread.

    ``` py
    # threading_example.py
    # By running the script below, you can observe the creation of four threads:

    import threading
    import time
    from concurrent.futures import ThreadPoolExecutor

    # Prints the values zero to two that your for loop assigns to the loop variable number. 
    def threaded_function():
        for number in range(3):
            # .name attribute on threading.current_thread() is used to get the name of the current thread. 
            # Helps with identifying which thread is executed each time. 
            print(f"Printing from {threading.current_thread().name}. {number=}")
            time.sleep(0.1)

    # Via ThreadPoolExecutor, four threads are created to execute the threaded function. 
    with ThreadPoolExecutor(max_workers=4, thread_name_prefix="Worker") as executor:
        for _ in range(4):
            executor.submit(threaded_function)
            
    # Output after running the script:
    # Each line represents a print() call from a worker thread and the number that 
    # follows the worker thread name shows the current iteration of the loop each thread is executing.
    # Each thread takes turns executing `threaded_function`, and the execution happens 
    # in a concurrent rather than sequential manner.
    Printing from Worker_0. number=0
    Printing from Worker_1. number=0
    Printing from Worker_2. number=0
    Printing from Worker_3. number=0
    Printing from Worker_0. number=1
    Printing from Worker_2. number=1
    Printing from Worker_1. number=1
    Printing from Worker_3. number=1
    Printing from Worker_0. number=2
    Printing from Worker_2. number=2
    Printing from Worker_1. number=2
    Printing from Worker_3. number=2

    ```

#### The context switch

- Notice from these interleaved outputs that multiple threads are running at the same time, taking turns to execute their part of the code.
- This happens because the Python interpreter performs a context switch
  - Python pauses the execution state of the current thread and passes control to another thread.
  - When the context switches, Python saves the current execution state so that it can resume later.
- To check the context switch interval of your Python interpreter by typing the following in the REPL:

  ``` sh
  >>> import sys
  >>> sys.getswitchinterval() # shows how often the Python interpreter considers switching to another thread.
  0.005
  ```

- The switch interval is defined in the Python docs as follows:
    > This floating-point value determines the ideal duration of the “timeslices” allocated to concurrently running Python threads. (Source)

- In the previous thread pool example, you’ll find a call to sleep() inside the threaded_function, which delays the program execution by 0.1 seconds. This increases the chance of a context switch happening in between because the execution will take much longer than the context switch interval.
- Due to context switching, programs can behave unexpectedly when run in a multithreaded environment.

### What is Thread Safety

- Thread safety refers to the property of an algorithm or program being able to function correctly during simultaneous execution by multiple threads.
- **Code is considered thread-safe if it behaves deterministically and produces the desired output when run in a multithreaded environment.**
- Thread safety issues occur because of two factors:
  - **Shared mutable data**: Threads share the memory of their parent process, so all variables and data structures are shared across threads. This can lead to errors when working with shared, changeable data.
  - **Non-atomic operations**: These occur in a multithreaded environment when operations involving multiple steps are interrupted by context switches. This can result in unexpected outcomes if threads are switched during the operation.

### The GIL and Its Implications on Threading

- Python’s Global Interpreter Lock (GIL) is a mutex that protects access to Python objects, preventing multiple threads from executing Python bytecodes simultaneously.
- The GIL allows only one thread to execute at a single point in time.
- This can lead to performance penalties if you try to use multithreading in CPU-bound programs.

#### Atomic Operations and Thread Safety

- When an operation is completed in a single bytecode instruction, it’s atomic.
- Because the GIL only allows one thread to run at a time, these atomic operations are safe from interference by other threads.
- This ensures that atomic operations are generally thread-safe, which means that you don’t have to worry about conflicts between threads with atomic operations.
- Also, keep in mind that the GIL only deals with single bytecode instructions, **which aren’t the same as single lines of Python code.**
  
> Note:
>
> - The GIL can be released during I/O operations or when calling C extensions, which can result in race conditions even for operations that appear atomic in Python code.
> Therefore, synchronization mechanisms like locks are still necessary to ensure thread safety when accessing shared mutable data.

#### Race Conditions

- A race condition occurs when **the outcome of a program depends on the sequence or timing of uncontrollable events** like thread execution order.
- Race conditions can lead to logical errors and non-deterministic results when code is run.
- If two threads simultaneously read and write to a shared variable without adequate synchronization, then they can interfere with each other, leading to incorrect results and behaviors.

    ``` py
    # bank_multithreaded_withdrawal.py
    import time
    from concurrent.futures import ThreadPoolExecutor

    class BankAccount:
        def __init__(self, balance=0):
            self.balance = balance

        def withdraw(self, amount):
            if self.balance >= amount:
                new_balance = self.balance - amount
                time.sleep(0.1)  # Simulate a delay
                self.balance = new_balance
            else:
                raise ValueError("Insufficient balance")

    account = BankAccount(1000)

    with ThreadPoolExecutor(max_workers=2) as executor:
        executor.submit(account.withdraw, 500)
        executor.submit(account.withdraw, 700)

    print(f"Final account balance: {account.balance}")
    
    # The result isn’t guaranteed to be deterministic. 
    # When you run this script multiple times, the results can vary:
    # You may get this result:
    Final account balance: 300
    
    # Or you may get this result instead
    Final account balance: 500
    ```

  - The code is expected to raise an exception since the account balance is only 1000 and is less than the total amount to be withdrawn, which is 1200—the sum of 500 and 700.
  - In the multithreaded scenario above, the flow of execution may happen as follows:
    - The calls to executor.submit() results in the creation of two threads.
    - The first thread checks the balance (1000) and finds it sufficient for a withdrawal of 500, so it proceeds with the withdrawal.
    - Before the first thread saves the update balance to the .balance attribute, a context switch happens, and the second thread starts and attempts to withdraw an amount of 700. It checks the balance and finds it sufficient for the withdrawal of 700.
    - Both threads independently calculate new balances based on the original balance of 1000. The first thread attempts to update the .balance attribute to 500, while the second thread, unaware of the first thread’s action, calculates and tries to set the balance to 300.
    - The thread that’s last to update “wins”, and sets the balance to either 300 or 500.
  - Notice that the error is due to simultaneous operations on **shared mutable data**, which in this case is the .balance attribute that’s shared between threads. Data is being read and modified by one thread while it’s being manipulated by another.

### Synchronization Primitives

- Python’s threading module provides various synchronization primitives to prevent race conditions and allow for coordination across threads.
- You can use synchronization primitives to do the following:
  - Control the simultaneous execution of a block of code by threads
  - Make multiple code statements atomic with respect to a thread
  - Limit concurrent access by threads
  - Coordinate between threads and perform actions based on the state of other threads
- Examples include:
  - Limiting Access
    - [[lang.py.basics.concurrency.threading.thread_safety.locks]]
    - [[lang.py.basics.concurrency.threading.thread_safety.semaphores]]
  - Communication and Coordination
    - [[lang.py.basics.concurrency.threading.thread_safety.events]]
    - [[lang.py.basics.concurrency.threading.thread_safety.conditions]]
    - [[lang.py.basics.concurrency.threading.thread_safety.barriers]]

### Deciding When to Use Synchronization Primitives

- When running code in a multithreaded environment, race conditions are common and result in unexpected and non-deterministic output.
- To make a program thread-safe, you need to know when to use synchronization primitives to control and coordinate threads.
- Some guidelines to help you decide when to use synchronization primitives when introducing multithreading in your applications:

##### Check for atomicity requirements

- Keep in mind that operations from different threads can interleave in unpredictable ways due to context switches.
- If a block of statements needs to be executed as an atomic unit, then you need to implement proper mutual exclusion synchronization primitives.

##### Check for shared mutable data

- When multiple threads operate on shared data, one thread may read the data that’s currently being modified by another thread.
- Either avoid sharing data by using thread-local storage, or if sharing is really necessary, introduce locks or other synchronization mechanisms to prevent race conditions.

##### External library code might not be designed for thread safety

- When writing code, you’ll often use third-party packages and methods from the standard-library modules.
- Keep in mind that library code might not be designed with thread safety in mind.
- The bottom line is that you’re responsible for ensuring thread safety by using proper synchronization primitives.

- When you’re unsure about the thread safety of a piece of code or operation, use appropriate synchronization primitives to safeguard against potential conflicts.
- As the CPython documentation wisely advises:

    ``` raw
    When in doubt, use a mutex. (Source)
    ```
