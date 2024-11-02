---
id: 7xaqoio81xtrbag4swi2vje
title: semaphores
desc: ''
updated: 1730391968935
created: 1730390384535
---



#### Limiting Access With Semaphores

- A semaphore is useful when the number of resources is limited and a number of threads try to access these limited resources. 
- It **uses a counter to limit access by multiple threads to a critical section.** 
- The `Semaphore()` constructor accepts a value argument, which denotes the maximum number of concurrent threads acquiring it.
- Semaphore objects have `.acquire()` and `.release()` methods and can be used as a context manager.
- Each `.acquire()` call reduces a semaphores’s counter by one, and further `.acquire()` calls are blocked when the counter reaches zero.
- When used as a context manager, 
  - the context manager block is entered after a successful `.acquire()` call. 
  - `.release()` is automatically called when the control exits the with block.
- An example where multiple customers are waiting in the bank to be served by a limited number of tellers.
  - `teller_semaphore` indicates the number of available tellers:

``` py
# bank_semaphore.py
import random
import threading
import time
from concurrent.futures import ThreadPoolExecutor

# Semaphore with a maximum of 2 resources (tellers)
teller_semaphore = threading.Semaphore(2)

def now():
    return time.strftime("%H:%M:%S")

# Simulates a customer’s interaction with a teller and does the following:
def serve_customer(name):
    print(f"{now()}: {name} is waiting for a teller.")
    with teller_semaphore:
        print(f"{now()}: {name} is being served by a teller.")
        # Simulate the time taken for the teller to serve the customer
        time.sleep(random.randint(1, 3))
        print(f"{now()}: {name} is done being served.")

customers = [
    "Customer 1",
    "Customer 2",
    "Customer 3",
    "Customer 4",
    "Customer 5",
]

with ThreadPoolExecutor(max_workers=5) as executor:
    for customer_name in customers:
        thread = executor.submit(serve_customer, customer_name)

print(f"{now()}: All customers have been served.")


# Output:
# Notice that initially, five customers are waiting at the bank to be served by tellers. 
# All customers start waiting at the same timestamp because the threads are created at roughly the same time.
10:12:28: Customer 1 is waiting for a teller.
10:12:28: Customer 1 is being served by a teller.
10:12:28: Customer 2 is waiting for a teller.
10:12:28: Customer 2 is being served by a teller.
10:12:28: Customer 3 is waiting for a teller.
10:12:28: Customer 4 is waiting for a teller.
10:12:28: Customer 5 is waiting for a teller.
10:12:29: Customer 1 is done being served.
10:12:29: Customer 3 is being served by a teller.
10:12:30: Customer 3 is done being served.
10:12:30: Customer 4 is being served by a teller.
10:12:31: Customer 2 is done being served.
10:12:31: Customer 5 is being served by a teller.
10:12:32: Customer 4 is done being served.
10:12:33: Customer 5 is done being served.
10:12:33: All customers have been served.
```

- Because `teller_semaphore` is acquired inside `serve_customer()`, the following sequence of events unfolds:
  - At any point in time, there are two active customers being served concurrently by the tellers. 
    - For e.g. at timestamp 10:12:28. Customers 1 and 2 are chosen for service by the tellers, and the other customers have to wait.
    - Remaining customers keep waiting and are admitted one at a time whenever an existing customer is done being served. 
      - At timestamp 10:12:29, Customer 1 is done being served. This frees up a teller, and Customer 3, who was waiting, gets the chance to be served.
- Using Semaphore here ensures that no more than two customers are being served at any given time, which effectively manages the limited teller resources.
