---
id: 34f629k9tdtdj6ggjvhsquo
title: conditions
desc: ''
updated: 1730391946817
created: 1730391937638
---


#### Conditions for Conditional Waiting

- A Condition object is built on top of a Lock or RLock object.
- It supports additional methods that **allow threads to wait for certain conditions to be met, and to signal other threads that those conditions have changed.**
- Condition objects are always associated with a lock.
  - The lock argument used in the `Condition()` constructor accepts either a Lock or RLock object.
  - If this argument is omitted, a new RLock object is created and used as the underlying lock.
- The various Condition methods can be used to coordinate across threads, allowing you to effectively manage the flow of execution in a multithreaded environment.
  - `.acquire()`: Used to acquire the underlying lock associated with the Condition. It must be called before a thread can wait on or signal a condition.
  - `.release()`: Releases the underlying lock.
  - `.wait(timeout=None)`: Blocks the thread until it’s notified or a specified timeout occurs. This method releases the lock before blocking and reacquires it upon notification or when the timeout expires. It’s used when a thread needs to wait for a specific condition to be true before proceeding.
  - `.notify(n=1)`: Wakes up one of the threads waiting for the condition if any are waiting. If multiple threads are waiting, the method selects one to notify at random.
  - `.notify_all()`: Wakes up all threads waiting for the condition. It’s the broadest way to handle notifications, ensuring that all waiting threads are notified. It’s useful when a change affects all waiting threads or when all threads need to recheck the condition they’re waiting on.
- By using these methods, you can
  - ensure that threads wait for specific conditions to be met before proceeding,
  - notify one or multiple threads when a condition has changed, and
  - maintain control over the sequence and timing of thread operations.
- Condition objects support the context manager protocol. I
- An example using a Condition object, `customer_available_condition`, which notifies the bank teller of the presence of a new customer:

In this example, you can see that the Condition object customer_available_condition acts as both a lock and a way to communicate between threads. It’s used to coordinate between the teller and customers.

Here, the customer_queue is the shared resource protected by the condition. The Condition object is used with the with statement to ensure that the Condition object’s lock is properly acquired and released.

Notice how serve_customers() runs in an infinite loop and performs the following functions:

Waits for customers using customer_available_condition.wait().
Uses the .wait() method and releases the Condition object’s lock. Because of this, add_customer_to_queue() can acquire the lock when a new customer arrives.
Simulates the action of the teller by serving customers when they arrive with a random delay representing the service time.
The add_customer_to_queue() function adds the customer to the queue and notifies the teller of a new customer using .notify().

You can see that the script creates a ThreadPoolExecutor with a maximum of six workers and calls serve_customers() from one thread. Then, it calls add_customer_to_queue() from five threads, representing five different customers in the customer_names list.
``` py
# bank_condition.py
import random
import threading
import time
from concurrent.futures import ThreadPoolExecutor

customer_available_condition = threading.Condition()

# Customers waiting to be served by the Teller

customer_queue = []

def now():
    return time.strftime("%H:%M:%S")

def serve_customers():
    while True:
        with customer_available_condition:
            # Wait for a customer to arrive
            while not customer_queue:
                print(f"{now()}: Teller is waiting for a customer.")
                customer_available_condition.wait()

            # Serve the customer
            customer = customer_queue.pop(0)
            print(f"{now()}: Teller is serving {customer}.")

        # Simulate the time taken to serve the customer
        time.sleep(random.randint(1, 5))
        print(f"{now()}: Teller has finished serving {customer}.")

def add_customer_to_queue(name):
    with customer_available_condition:
        print(f"{now()}: {name} has arrived at the bank.")
        customer_queue.append(name)

        customer_available_condition.notify()

customer_names = [
    "Customer 1",
    "Customer 2",
    "Customer 3",
    "Customer 4",
    "Customer 5",
]

with ThreadPoolExecutor(max_workers=6) as executor:
    teller_thread = executor.submit(serve_customers)
    for name in customer_names:
        # Simulate customers arriving at random intervals
        time.sleep(random.randint(1, 3))
        executor.submit(add_customer_to_queue, name)
This script generates the following output:

$ python bank_condition.py
10:15:08: Teller is waiting for a customer.
10:15:09: Customer 1 has arrived at the bank.
10:15:09: Teller is serving Customer 1.
10:15:11: Customer 2 has arrived at the bank.
10:15:12: Teller has finished serving Customer 1.
10:15:12: Teller is serving Customer 2.
10:15:13: Teller has finished serving Customer 2.
10:15:13: Teller is waiting for a customer.
10:15:14: Customer 3 has arrived at the bank.
10:15:14: Teller is serving Customer 3.
10:15:15: Customer 4 has arrived at the bank.
10:15:17: Customer 5 has arrived at the bank.
10:15:18: Teller has finished serving Customer 3.
10:15:18: Teller is serving Customer 4.
10:15:22: Teller has finished serving Customer 4.
10:15:22: Teller is serving Customer 5.
10:15:25: Teller has finished serving Customer 5.
10:15:25: Teller is waiting for a customer.
```

- Using Condition here has allowed for:
  - The teller to efficiently wait for customers without busy-waiting
  - Customers to notify the teller of their arrival
  - Synchronization of access to the shared customer queue
- **Condition is commonly used in producer-consumer scenarios**. In this case, the customers are producers adding to the queue, and the teller is a consumer taking from the queue.