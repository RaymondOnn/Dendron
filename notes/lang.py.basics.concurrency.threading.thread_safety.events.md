---
id: d4crrh57rfqmmwu25n8yunh
title: events
desc: ''
updated: 1730391954775
created: 1730391818200
---


### Events for Signaling

- Event objects can be used for signaling, **allowing a thread to notify one or more threads about an action**.
- An Event object can be created by instantiating Event from the threading module.
- Event objects maintain an internal flag that starts as `False`. Can set to `True` with `.set()` and reset it to `False` with `.clear()`.
- Threads can wait for the flag to become `True` using `.wait()`, which blocks the thread until the flag is set.

- An example of an Event object in action in the banking scenario below:

``` py
# bank_event.py
import threading
import time
from concurrent.futures import ThreadPoolExecutor

bank_open = threading.Event() # Signals when the bank is open.
transactions_open = threading.Event() # Signals when transactions are allowed.

def serve_customer(customer_data):
    print(f"{customer_data['name']} is waiting for the bank to open.")

    bank_open.wait()
    print(f"{customer_data['name']} entered the bank")
    if customer_data["type"] == "WITHDRAW_MONEY":
        print(f"{customer_data['name']} is waiting for transactions to open.")
        transactions_open.wait()
        print(f"{customer_data['name']} is starting their transaction.")

        # Simulate the time taken for performing the transaction
        time.sleep(2)

        print(
            f"{customer_data['name']} completed transaction and exited bank"
        )
    else:
        # Simulate the time taken for banking
        time.sleep(2)
        print(f"{customer_data['name']} has exited bank")

customers = [
    {"name": "Customer 1", "type": "WITHDRAW_MONEY"},
    {"name": "Customer 2", "type": "CHECK_BALANCE"},
    {"name": "Customer 3", "type": "WITHDRAW_MONEY"},
    {"name": "Customer 4", "type": "WITHDRAW_MONEY"},
]

with ThreadPoolExecutor(max_workers=4) as executor:
    for customer_data in customers:
        executor.submit(serve_customer, customer_data)

    print("Bank manager is preparing to open the bank.")
    time.sleep(2)
    print("Bank is now open!")
    bank_open.set()  # Signal that the bank is open

    time.sleep(3)
    print("Transactions are now open!")
    transactions_open.set()

print("All customers have completed their transactions.")

# Here’s what the output looks like now:
Customer 1 is waiting for the bank to open.
Customer 2 is waiting for the bank to open.
Customer 3 is waiting for the bank to open.
Customer 4 is waiting for the bank to open.
Bank manager is preparing to open the bank.
Bank is now open!
Customer 1 entered the bank
Customer 4 entered the bank
Customer 3 entered the bank
Customer 3 is waiting for transactions to open.
Customer 1 is waiting for transactions to open.
Customer 2 entered the bank
Customer 4 is waiting for transactions to open.
Customer 2 has exited bank
Transactions are now open!
Customer 4 is starting their transaction.
Customer 3 is starting their transaction.
Customer 1 is starting their transaction.
Customer 3 completed transaction and exited bank
Customer 1 completed transaction and exited bank
Customer 4 completed transaction and exited bank
All customers have completed their transactions.
```

- This example illustrates how you can use Event objects to coordinate actions across multiple threads.
- Events are great in scenarios where you need to signal state changes to multiple waiting threads simultaneously.
- This provides an efficient way to manage synchronization in your programs.