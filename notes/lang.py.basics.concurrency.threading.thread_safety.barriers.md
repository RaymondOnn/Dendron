---
id: hp0skqt4h3tchhqmh3aria9
title: barriers
desc: ''
updated: 1730392719828
created: 1730392711451
---

#### Barriers for Coordination

- A barrier is a synchronization primitive that allows a group of threads to wait for each other before continuing execution.
- You can use Barrier objects to **block program execution until a specified number of threads have reached the barrier point**.
- Barrier in Python’s threading module has the following signature:

    ``` py
    Barrier(parties, action=None, timeout=None)
    ```

  - It has one required and two optional arguments:
    - `parties`: specifies the number of threads of the Barrier object. The `.wait()` method waits for this number of threads to reach the barrier point before proceeding.
    - `action` is an optional callable that will be called by one of the threads when it’s released.
    - `timeout` optionally specifies the timeout value for the .wait() method.
- A Barrier object can be used in a banking scenario when you want to accept all customers into a bank only after all the bank tellers are ready. In this example, you’ll see that the variable teller_barrier holds a Barrier object initialized with three parties:

    ``` py
    # bank_barrier.py
    import random
    import threading
    import time
    from concurrent.futures import ThreadPoolExecutor

    # Ensures that all three tellers must first be ready before serving customers. 
    teller_barrier = threading.Barrier(3)

    def now():
        return time.strftime("%H:%M:%S")

    def prepare_for_work(name):
        print(f"{now()}: {name} is preparing their counter.")

        # Simulate the delay to prepare the counter. Different tellers take different time to prepare.
        time.sleep(random.randint(1, 3))
        print(f"{now()}: {name} has finished preparing.")

        # Wait for the other threads i.e. tellers to finish preparing. 
        teller_barrier.wait()
        print(f"{now()}: {name} is now ready to serve customers.")

    tellers = ["Teller 1", "Teller 2", "Teller 3"]

    with ThreadPoolExecutor(max_workers=3) as executor:
        for teller_name in tellers:
            executor.submit(prepare_for_work, teller_name)

    print(f"{now()}: All tellers are ready to serve customers.")


    # Output:
    
    # Tellers start preparing at roughly the same time, as shown by the timestamp 10:19:42.
    10:19:42: Teller 1 is preparing their counter.
    10:19:42: Teller 2 is preparing their counter.
    10:19:42: Teller 3 is preparing their counter.
    
    # They finish preparing at different times due to random delays. 
    # Notice that Teller 1 takes longer to finish than the others.
    10:19:43: Teller 2 has finished preparing.
    10:19:43: Teller 3 has finished preparing.
    10:19:44: Teller 1 has finished preparing.
    
    10:19:44: Teller 1 is now ready to serve customers.
    10:19:44: Teller 3 is now ready to serve customers.
    10:19:44: Teller 2 is now ready to serve customers.
    
    # Once the last teller finishes preparing at timestamp 10:19:44, all tellers announce they’re ready to serve customers at the same timestamp.
    10:19:44: All tellers are ready to serve customers.
    ```

- Barrier objects are **useful in scenarios where multiple threads need to wait for each other before proceeding, or when you need to synchronize the start of a particular phase across multiple threads**.