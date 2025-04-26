---
id: s3mbgrxkc6il0txc9lk6bf7
title: Singleton
desc: ''
updated: 1743949365419
created: 1741507689787
---

## [Singleton Pattern](https://python.plainenglish.io/singleton-pattern-in-python-managing-global-state-with-thread-safety-166d21279904)

### What is the Singleton Pattern?

- The Singleton Pattern restricts the instantiation of a class to only one instance and ensures all parts of the application access the class via the same instance.
- Useful for managing shared resources or global states i.e. configuration manager, logging system, or database connection pool.

### Why Use Singleton?

- **Global State Management**: Ensures that there is only one instance controlling critical resources.
- **Lazy Initialization**: The instance is created only when needed, reducing overhead.
- **Controlled Access**: Helps ensure that the class is accessed in a controlled manner across different parts of the application.
- In a multi-threaded environment, thread-safety must be ensured to prevent issues like race conditions where multiple threads attempt to access or modify the singleton instance simultaneously.

### Implementing Singleton in Python

#### Approach 1: Singleton Using Metaclass

- Metaclasses are classes that define the behavior of other classes.
- Using a metaclass, we can ensure that only one instance of a class is created.

``` py
# Ensures only one instance of ConfigurationManager can be created.
class SingletonMeta(type):
    _instances = {} # tracks the instances

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            instance = super(SingletonMeta, cls).__call__(*args, **kwargs)
            cls._instances[cls] = instance
        return cls._instances[cls]

class ConfigurationManager(metaclass=SingletonMeta):
    def __init__(self, config=None):
        if not hasattr(self, 'initialized'):  # Check if the instance has already been initialized
            self.config = config if config else {}
            self.initialized = True

# Example Usage

config1 = ConfigurationManager({"host": "localhost", "port": 8080})
config2 = ConfigurationManager({"host": "127.0.0.1", "port": 9090})

print(config1 is config2)  # True, as both references point to the same instance
```

#### Approach 2: Singleton Using `__new__` Method with `threading.Lock`

- A more common and simpler way to implement the Singleton in Python is using the `__new__` method.
- This method controls the creation of a new instance.
- When combined with `threading.Lock`, we can ensure thread-safety.

``` py
import threading

class ConfigurationManager:
    _instance = None
    _lock = threading.Lock()

    def __new__(cls, config=None):
        with cls._lock:
            if cls._instance is None:
                cls._instance = super().__new__(cls)
                cls._instance.config = config if config else {}
        return cls._instance

# Example Usage

config1 = ConfigurationManager({"host": "localhost", "port": 8080})
config2 = ConfigurationManager({"host": "127.0.0.1", "port": 9090})

print(config1 is config2)  # True
```

Explanation:

- `__new__` is responsible for ensuring that only one instance of the ConfigurationManager class exists.
- 'threading.Lock' is used to ensure that the class is thread-safe.
  - If multiple threads try to create an instance of the Singleton at the same time, the lock ensures that only one thread can do so.

##### Advanced Thread-Safety Considerations

###### Thread Contention

- When multiple threads attempt to acquire a lock at the same time, some threads will be forced to wait, potentially leading to performance bottlenecks. This can become an issue when the lock is held for a long duration or when many threads need to acquire it frequently.
- To mitigate this, one alternative approach is using **Read-Write Locks**.
  - This allows multiple threads to read from the Singleton concurrently but ensures that only one thread can write to it at a time.
  - In Python, the threading.RLock (recursive lock) or libraries like threading.Condition can be used to implement more complex locking mechanisms.

###### Performance Considerations

- While locks ensure thread-safety, they can slow down performance in highly concurrent environments.
- The threading.Lock approach might be a good choice in environments with low to moderate concurrency, but it may not scale well when the number of threads increases significantly.
- In high-concurrency environments, the Global Interpreter Lock (GIL) in Python can also impact performance.
- Some alternative strategies for improving performance in these situations include:
  - Caching Mechanisms: Implementing caching in conjunction with Singleton can reduce the frequency of locks.
  - Lazy Initialization: Instead of acquiring a lock on every access, Singleton instances can be lazily initialized, only when they are needed.
  -

### Testing Singleton Implementations

- Testing the Singleton pattern can be challenging, especially when managing global state.
- Some potential issues include:

  - Mocking: In unit tests, mocking Singleton instances can be difficult. A Singleton’s global nature makes it difficult to isolate it in tests without affecting other tests.
  - State Management: If Singleton holds mutable state, it can create unexpected results in tests, especially if tests don’t reset the Singleton instance between test runs.

#### Solutions for Testing

- Mocking: Use libraries like unittest.mock to mock Singleton instances and ensure they do not interfere with the global state during testing.
- Resetting Singleton: For each test, the Singleton instance can be reset to ensure that no state is carried over.

``` py
from unittest.mock import patch

def test_singleton():
    with patch.object(ConfigurationManager, '_instance', None):
        config1 = ConfigurationManager()
        config2 = ConfigurationManager()
        assert config1 is config2
```

### Scalability and Distributed Systems

- In Python, the threading.Lock ensures thread-safety only within a single process.
- However, in distributed systems or multi-process environments, this lock does not work across processes.
- If you need to ensure that a Singleton instance is maintained across different processes or machines, you'll need to use a distributed lock.

- One popular solution is to use Redis for distributed locks, allowing processes to synchronize access to the Singleton instance.
- Alternatively, a database-backed Singleton pattern can store the instance state in a shared database, ensuring consistency across processes and even servers.

``` py
import redis

class DistributedSingleton:
    _lock = redis.StrictRedis(host='localhost', port=6379, db=0)

    def __new__(cls):
        with cls._lock.lock("singleton_lock"):
            if not cls._instance:
                cls._instance = super().__new__(cls)
        return cls._instance
```
