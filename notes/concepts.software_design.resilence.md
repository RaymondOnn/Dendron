---
id: 7bguu6y24g5ppqm4a6iqodn
title: Resilence
desc: ''
updated: 1746353987360
created: 1746350109221
---

## Resilence Strategies

- Building resilient applications is a critical aspect of modern software engineering.
- Resilience strategies are essential to ensure that applications can gracefully handle failures, recover quickly, and provide a reliable experience for users.

### Retry Logic (Handling Temporary Failures)

- **Strategy:** When an operation fails due to transient issues (e.g., network failures, timeouts), retrying the operation can sometimes resolve the issue.

**Python Implementation:**

```python
import time
import random

def unreliable_task():
    """Simulate an unreliable task (e.g., network call)."""
    if random.random() < 0.7:
        raise Exception("Temporary failure")
    return "Success"

def retry_task(max_retries=3, delay=2):
    for attempt in range(max_retries):
        try:
            result = unreliable_task()
            return result
        except Exception as e:
            print(f"Attempt {attempt + 1} failed: {e}")
            if attempt < max_retries - 1:
                print(f"Retrying in {delay} seconds...")
                time.sleep(delay)
            else:
                print("Max retries reached. Giving up.")
                raise

# Test the retry mechanism
print(retry_task())
```

### Circuit Breaker (Preventing Resource Exhaustion)

**Strategy:** The circuit breaker pattern ensures that when an operation fails repeatedly, the system stops making calls to the failing service and allows it to recover. After a timeout, it attempts to call the service again.

**Python Implementation:**

```python
import time

class CircuitBreaker:
    def __init__(self, threshold=3, timeout=5):
        self.threshold = threshold  # Number of failures before opening the circuit
        self.timeout = timeout  # Time to wait before trying again
        self.failures = 0
        self.last_failed = None
        self.state = "CLOSED"  # Can be CLOSED, OPEN, or HALF_OPEN

    def call(self, func):
        if self.state == "OPEN":
            if time.time() - self.last_failed > self.timeout:
                print("Circuit breaker is now HALF_OPEN. Retrying operation.")
                self.state = "HALF_OPEN"
            else:
                print("Circuit is OPEN. Operation blocked.")
                raise Exception("Circuit is OPEN. Operation blocked.")

        try:
            result = func()
            self.failures = 0
            self.state = "CLOSED"
            return result
        except Exception as e:
            self.failures += 1
            self.last_failed = time.time()
            if self.failures >= self.threshold:
                self.state = "OPEN"
            raise e

def unreliable_service():
    """Simulate an unreliable service."""
    if random.random() < 0.5:
        raise Exception("Service failed")
    return "Service success"

# Using the Circuit Breaker
cb = CircuitBreaker()
for _ in range(5):
    try:
        print(cb.call(unreliable_service))
    except Exception as e:
        print(f"Error: {e}")
    time.sleep(1)
```

### 3. **Fallback Mechanism (Providing Default Responses)**

**Strategy:** When an operation fails, provide a fallback response or behavior instead of failing completely. This allows the system to keep functioning even if one part of it is down.

**Python Implementation:**

```python
import random                         

def unreliable_service():
    """Simulate an unreliable service."""
    if random.random() < 0.5:
        raise Exception("Service failure")
    return "Service success"

def fallback():
    """Provide a fallback response."""
    return "Fallback response"

def call_with_fallback():
    try:
        return unreliable_service()
    except Exception as e:
        print(f"Error: {e}. Using fallback.")
        return fallback()

# Test the fallback mechanism
print(call_with_fallback())
```

- Fallback Decorator

    ``` py
    from functools import wraps
    from inspect import isgeneratorfunction
    from typing import Callable, Type, Any


    class FallbackClass:
        fallback: Callable
        fallback_function: Callable
        expected_exception: Exception

        def __init__(self,
                    fallback: Callable = None,
                    fallback_function: Callable = None,
                    expected_exception: Type[BaseException] = Exception):
            self.fallback = fallback
            self.fallback_function = fallback_function
            self._expected_exception = expected_exception

            if not self.fallback and not self.fallback_function:
                raise TypeError(
                    "Fallback decorator requires either a \"fallback\" or \"fallback_exception\" argument and "
                    "neither was given.")
            if self.fallback and not callable(self.fallback):
                raise TypeError(
                    "Argument \"fallback\" must be callable (i.e. a function)")
            if self.fallback_function and not callable(self.fallback_function):
                raise TypeError(
                    "Argument \"fallback_exception\" must be callable (i.e. a function)"
                )

        def __call__(self, decorated_function):
            return self.decorate(decorated_function)

        @staticmethod
        def call(func, *args, **kwargs) -> Any:
            return func(*args, **kwargs)

        @staticmethod
        def call_generator(func, *args, **kwargs):
            for el in func(*args, **kwargs):
                yield el

        def decorate(self, function_to_decorate: Callable = None) -> Callable:
            call = self.call_generator if isgeneratorfunction(
                function_to_decorate) else self.call

            @wraps(function_to_decorate)
            def wrapper(*args, **kwargs):
                return self.try_catch_fallback(call, function_to_decorate, *args,
                                            **kwargs)

            return wrapper

        def try_catch_fallback(self, call, function_to_decorate, *args, **kwargs):
            try:
                return call(function_to_decorate, *args, **kwargs)
            except Exception as e:
                if issubclass(e.__class__, self._expected_exception):
                    if self.fallback_function:
                        return call(self.fallback_function, (e, *args), **kwargs)
                    else:
                        return call(self.fallback, *args, **kwargs)
                else:
                    raise e

    def WithFallback(fallback: Callable = None,
                    fallback_function: Callable = None,
                    for_exception: Type[BaseException] = Exception):
        return FallbackClass(fallback=fallback,
                            fallback_function=fallback_function,
                            expected_exception=for_exception)

    if __name__ == '__main__':
        def fallback_exception(exception, foo):
            print("Original argument:", foo, "- Exception:", exception)


        def fallback(foo):
            print("Original argument:", foo)


        @WithFallback(fallback_exception=fallback_exception)
        def some_function(foo):
            # Try to do something and fail
            raise ConnectionError()


        @WithFallback(fallback)
        def some_other_function(foo):
            # Try to do something and fail
            raise ConnectionError()
    ```

### 4. **Timeout (Preventing Hanging Requests)**

- **Strategy:**  Setting a timeout for external calls ensures that if the operation doesn't complete in the expected time frame, it will be aborted and allow the system to take corrective actions.

**Python Implementation:**

```python
import signal
import time

# Custom timeout exception
class TimeoutException(Exception):
    pass

def timeout_handler(signum, frame):
    raise TimeoutException("Operation timed out")

# Function that takes a long time to complete
def long_running_task():
    time.sleep(5)
    return "Task completed"

# Applying the timeout
def run_with_timeout(func, timeout=3):
    signal.signal(signal.SIGALRM, timeout_handler)
    signal.alarm(timeout)  # Set the timeout
    try:
        return func()
    except TimeoutException:
        print("The operation took too long and timed out.")
        return None
    finally:
        signal.alarm(0)  # Disable the alarm

# Test the timeout mechanism
print(run_with_timeout(long_running_task))
```

### 5. **Bulkhead (Isolating Failure Domains)**

**Strategy:** Bulkhead is a pattern that isolates different parts of a system to prevent a failure in one part from affecting the entire system.

**Python Implementation:**

```python
import threading
import time

class Bulkhead:
    def __init__(self, max_threads=2):
        self.max_threads = max_threads
        self.lock = threading.Semaphore(max_threads)

    def execute(self, func, *args, **kwargs):
        with self.lock:
            return func(*args, **kwargs)

def unreliable_task(task_id):
    """Simulate unreliable task."""
    if random.random() < 0.5:
        raise Exception(f"Task {task_id} failed")
    return f"Task {task_id} succeeded"

bulkhead = Bulkhead(max_threads=2)

def run_tasks():
    tasks = [lambda: unreliable_task(i) for i in range(5)]
    threads = []
    
    for task in tasks:
        thread = threading.Thread(target=lambda: print(bulkhead.execute(task)))
        threads.append(thread)
        thread.start()
    
    for thread in threads:
        thread.join()

# Test the bulkhead mechanism (limited concurrency)
run_tasks()
```

### 6. **Load Shedding (Rejecting Requests During Overload)**

**Strategy:** In high-traffic situations, the system rejects incoming requests to prevent overloading and to ensure that the system continues to function within its limits.

**Python Implementation:**

```python
import random
import time

class LoadShedding:
    def __init__(self, max_requests=5):
        self.max_requests = max_requests
        self.current_requests = 0

    def handle_request(self, func, *args, **kwargs):
        if self.current_requests >= self.max_requests:
            print("Too many requests, shedding load...")
            return "Service unavailable"
        
        self.current_requests += 1
        try:
            return func(*args, **kwargs)
        finally:
            self.current_requests -= 1

def service_request():
    """Simulate service request."""
    time.sleep(1)
    return "Service response"

# Test the load shedding mechanism
shedding = LoadShedding(max_requests=3)

def simulate_load():
    for i in range(7):
        print(f"Request {i + 1}: {shedding.handle_request(service_request)}")

simulate_load()
```

### 7. **Graceful Degradation (Reduced Functionality under Stress)**

**Strategy:** When the system is under stress or when resources are constrained, the system may degrade gracefully by limiting certain features or reducing the load on critical services.

**Python Implementation:**

```python
class GracefulDegradation:
    def __init__(self, enable_full_service=True):
        self.enable_full_service = enable_full_service

    def service(self):
        if not self.enable_full_service:
            print("System is in degraded mode. Providing limited functionality.")
            return "Basic service"
        else:
            print("System is in full service mode.")
            return "Full service response"

# Simulate degraded mode
degraded_system = GracefulDegradation(enable_full_service=False)
print(degraded_system.service())

# Simulate full service mode
full_system = GracefulDegradation(enable_full_service=True)
print(full_system.service())
```

