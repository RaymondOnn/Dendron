---
id: wibgblixug0u0mxvyq6vidu
title: locks
desc: ''
updated: 1730391962663
created: 1730389061631
---

#### Using Python Threading Locks for Mutual Exclusion

- A lock is a synchronization primitive that can be **used for exclusive access to a resource**.
- Once a thread acquires a lock, no other threads can acquire it and proceed until the lock is released.
- You can use a lock to wrap a statement or group of statements that should be executed atomically.

#### `threading.Lock` for Primitive Locking

- Create a `Lock` object by calling the `Lock()` constructor from Python’s threading module.
- A `Lock` object has two states: **`LOCKED`** and **`UNLOCKED`**.
  - **`UNLOCKED`**,
    - The `Lock` object can be acquired by a thread by calling the `.acquire()` method on Lock.
    - The lock is then held by the thread and other threads can’t access it.
    - `.acquire()`:
      - When the Lock object state is unlocked, `.acquire()` changes the Lock object to a locked state and returns immediately.
      - If the Lock object is in a locked state, `.acquire()` blocks the program execution of other threads and waits for the Lock object to be released by the thread holding the lock.
  - **`LOCKED`**
    - the Lock object is released by calling the `.release()` method so other threads can acquire it.
    - `.release()`:
      - When the Lock object state is locked, the `.acquire()` method calls from other threads will block their execution until the thread holding the lock calls `.release()` on Lock.
      - It should only be called in the locked state because it changes the state to unlocked and returns immediately.
      - If an attempt is made to release an unlocked lock, a RuntimeError is raised.

- The Lock object can be used as a context manager when used with the `with` statement.
  - This automates the acquiring and releasing of locks.
  - When the program enters the with block, the `.acquire()` method on the Lock is automatically called.
  - When the program exits the with block, the `.release()` method is called.

``` py
# bank_thread_safe.py
# Adding a lock to make it thread-safe:
import threading
import time
from concurrent.futures import ThreadPoolExecutor

class BankAccount:
    def **init**(self, balance=0):
        self.balance = balance
        self.account_lock = threading.Lock()

    def withdraw(self, amount):
        with self.account_lock: # only one thread can execute at any one time
            if self.balance >= amount:
                new_balance = self.balance - amount
                print(f"Withdrawing {amount}...")
                time.sleep(0.1)  # Simulate a delay
                self.balance = new_balance
            else:
                raise ValueError("Insufficient balance")

    def deposit(self, amount):
        with self.account_lock:  # only one thread can execute at any one time
            new_balance = self.balance + amount
            print(f"Depositing {amount}...")
            time.sleep(0.1)  # Simulate a delay
            self.balance = new_balance

account = BankAccount(1000)

with ThreadPoolExecutor(max_workers=3) as executor:
    executor.submit(account.withdraw, 700)
    executor.submit(account.deposit, 1000)
    executor.submit(account.withdraw, 300)

print(f"Final account balance: {account.balance}")
```

- In this example, you’ll notice that a BankAccount instance and three threads are created:
  - The first thread withdraws 700.
  - The second thread deposits 1000.
  - The third thread withdraws 300.
  - For additional insight, you add a descriptive call to `print()` to both methods and also print the final account balance after all the threads are complete.

    ``` sh
    $ python bank_thread_safe.py
    Withdrawing 700...
    Depositing 1000...
    Withdrawing 300...
    Final account balance: 1000
    ```

- This implementation is thread-safe.
- Lock ensures that only one thread at a time can modify the balance across the `deposit()` and `withdraw()` functions, both of which share the same lock.
- The `with self.account_lock:` statement acquires the lock before entering the block and releases it after exiting. This solves the race condition problem you saw in the earlier example.
- Operations on the balance are atomic across threads, and they can’t be interrupted by other threads once they’ve started i.e. threads will wait for a pending withdrawal or deposit operation to complete before it executes its operation.

#### threading.RLock for Reentrant Locking

- If a lock isn’t released properly due to an error or oversight in the code, it can lead to a deadlock, where threads wait indefinitely for the lock to be released.
- The reasons for a deadlock include:
  - **Nested Lock Acquisition**: A deadlock can occur if a thread attempts to acquire a lock it already holds.
    - In conventional locks, trying to acquire the same lock multiple times within the same thread leads to the thread blocking itself, a situation that doesn’t resolve without external intervention.
  - **Multiple Locks Acquisition**: A deadlock is likely when multiple locks are used, and threads acquire them in inconsistent order.
    - If two threads each hold one lock and are waiting for the other, neither thread can proceed, resulting in a deadlock.

- A nested lock acquisition example.
  - Notice how BankAccount now includes two methods that execute when you perform a cash deposit:

``` py
# bank_deadlock.py
import threading
import time
from concurrent.futures import ThreadPoolExecutor

class BankAccount:
    def **init**(self):
        self.balance = 0
        self.lock = threading.Lock()

    def deposit(self, amount):
        print(
            f"Thread {threading.current_thread().name} "
            "waiting to acquire lock for .deposit()"
        )
        with self.lock:
            print(
                f"Thread {threading.current_thread().name} "
                "acquired lock for .deposit()"
            )
            time.sleep(0.1)
            self._update_balance(amount)

    def _update_balance(self, amount):
        print(
            f"Thread {threading.current_thread().name} "
            "waiting to acquire lock for ._update_balance()"
        )
        with self.lock:  # This will cause a deadlock
            print(
                f"Thread {threading.current_thread().name} "
                "acquired lock for ._update_balance()"
            )
            self.balance += amount

account = BankAccount()

with ThreadPoolExecutor(max_workers=3, thread_name_prefix="Worker") as executor:
    for_ in range(3):
        executor.submit(account.deposit, 100)

print(f"Final balance: {account.balance}")

# Running this script leads to a deadlock scenario:
# Notice that the execution is stopped due to a deadlock and the interpreter locks infinitely. 
Thread Worker_0 waiting to acquire lock for .deposit()
Thread Worker_0 acquired lock for .deposit()
Thread Worker_1 waiting to acquire lock for .deposit()
Thread Worker_2 waiting to acquire lock for .deposit()
Thread Worker_0 waiting to acquire lock for ._update_balance()
```

- The quick breakdown below explains what’s happening:
  - Thread `Worker_0` acquires the lock in the `.deposit()` method.
  - The same thread then tries to acquire the lock again in `._update_balance()`.
  - Since the lock is non-reentrant, meaning it can’t be acquired again by the same thread, the program deadlocks.
  - Threads `Worker_1` and `Worker_2` are waiting to acquire the lock, but they’ll never get it because the first thread, `Worker_0`, is deadlocked.
  - The lock objects created from `threading.Lock` are non-reentrant.
    - Once a thread has acquired it, that same thread can’t acquire it again without first releasing it.
    - The thread hangs indefinitely because `._update_balance()` tries to acquire the lock that’s already held by `.deposit()`.

##### The solution to deadlocks: `RLock`

- This deadlock issue can be fixed by using `RLock`, which is a reentrant lock. **It doesn’t block when a holding thread requests the lock again**.
- In other words, an `RLock` allows a thread to acquire the lock multiple times before it releases the lock.
- This is useful in recursive functions or in situations where a thread needs to re-enter a locked resource that it has already locked.
- Similar to `Lock`, an `RLock` object can be created by instantiating `RLock` from the threading module in the Python standard library. You can use RLock to prevent a deadlock scenario:

``` py
# bank_rlock.py
import threading
import time
from concurrent.futures import ThreadPoolExecutor

class BankAccount:
    def **init**(self):
        self.balance = 0
        self.lock = threading.RLock()

    def deposit(self, amount):
        print(
            f"Thread {threading.current_thread().name} "
            "waiting to acquire lock for .deposit()"
        )
        with self.lock:
            print(
                f"Thread {threading.current_thread().name} "
                "acquired lock for .deposit()"
            )
            time.sleep(0.1)
            self._update_balance(amount)

    def _update_balance(self, amount):
        print(
            f"Thread {threading.current_thread().name} "
            "waiting to acquire lock for ._update_balance()"
        )
        with self.lock:
            print(
                f"Thread {threading.current_thread().name} "
                "acquired lock for ._update_balance()"
            )
            self.balance += amount

account = BankAccount()

with ThreadPoolExecutor(max_workers=3, thread_name_prefix="Worker") as executor:
    for_ in range(3):
        executor.submit(account.deposit, 100)

# Here, the .lock attribute is now an instance of RLock instead of Lock. 
# The script will now run without a deadlock:
# Notice that the execution is completed because the RLock object allows a thread to acquire the same lock multiple times. 
Thread Worker_0 waiting to acquire lock for .deposit()
Thread Worker_0 acquired lock for .deposit()
Thread Worker_1 waiting to acquire lock for .deposit()
Thread Worker_2 waiting to acquire lock for .deposit()
Thread Worker_0 waiting to acquire lock for ._update_balance()
Thread Worker_0 acquired lock for ._update_balance()
Thread Worker_1 acquired lock for .deposit()
Thread Worker_1 waiting to acquire lock for ._update_balance()
Thread Worker_1 acquired lock for ._update_balance()
Thread Worker_2 acquired lock for .deposit()
Thread Worker_2 waiting to acquire lock for ._update_balance()
Thread Worker_2 acquired lock for ._update_balance()
Final balance: 300
```

- RLock is helpful in scenarios like this, where you have nested lock acquisitions within the same thread.
- RLock also keeps a count of how many times it’s been acquired, and it must be released the same number of times to be fully unlocked.
- While RLock offers the advantage of allowing the same thread to acquire the lock multiple times without causing a deadlock, it comes with a slight performance overhead compared to Lock as RLock needs to track the number of times the same thread has acquired it.
- **If your use case doesn’t require a thread to re-acquire a lock it already holds, it’s more efficient to use Lock.**
