---
id: hg6rodjfsildenr54brldft
title: Mocking
desc: ""
updated: 1724172709985
created: 1713923429777
---
<https://nedbatchelder.com/blog/201908/why_your_mock_doesnt_work.html>
<https://medium.com/@benshearlaw/create-a-mock-sql-db-in-python-from-csv-for-unit-testing-6668a09dd360>

### What is Mocking

- A mock object substitutes and imitates a real object within a testing environment. It is a versatile and powerful tool for improving the quality of your tests.
- One reason to use Python mock objects is to control your code’s behavior during testing.
  - For e.g. if your code makes HTTP requests to external services, then your tests execute predictably only so far as the services are behaving as you expected.
  - Sometimes, a temporary change in the behavior of these external services can cause intermittent failures within your test suite.
  - Hence, it would be better to test your code in a controlled environment.
  - Replacing the actual request with a mock object allows you to simulate external service outages and successful responses in a predictable way.
- Using Python mock objects can help you control the execution path of your code, allowing us to test areas i.e. except blocks and if statements that are hard to satisfy.
- Mocking also helps you to better understand how you’re using their real counterparts in your code.
  - A Python mock object contains data about its usage that you can inspect such as:
    - If you called a method
    - How you called the method
    - How often you called the method

### The Mock Object

- `unittest.mock` offers a base class for mocking objects called `Mock`.
- The use cases for `Mock` are practically limitless because `Mock` is so flexible.

1. Begin by instantiating a new Mock instance:

    ``` py
    from unittest.mock import Mock
    from unittest.mock import call, create_autospec, MagicMock, Mock, patch
    mock = Mock()
    mock
    >>> <Mock id='4561344720'>
    ```

2. Now, you are able to substitute an object in your code with your new Mock.
You can do this by passing it as an argument to a function or by redefining another object:

    ``` py
    # Pass mock as an argument to do_something()
    do_something(mock)

    # Patch the json library
    json = mock
    ```

- When you substitute an object in your code, the Mock must look like the real object it is replacing.
  - Else, your code will not be able to use the Mock in place of the original object.
  - For e.g. if you are mocking the `json` library and your program calls `dumps()`, then your Python mock object must also contain `dumps()`.

#### Lazy Attributes and Methods

- A Mock must simulate any object that it replaces.
- To achieve such flexibility, it creates its attributes when you access them:

    ``` py
    >>> mock.some_attribute
    <Mock name='mock.some_attribute' id='4394778696'>
    >>> mock.do_something()
    <Mock name='mock.do_something()' id='4394778920'>
    ```

- Since Mock can create arbitrary attributes on the fly, it is suitable to replace any object.
  - if you’re mocking the `json` library and you call `dumps()`, the Python mock object will create the method so that its interface can match the library’s interface:

    ``` py
    >>> json = Mock()
    >>> json.dumps()
    <Mock name='mock.dumps()' id='4392249776'>
    ```

    - Unlike the real `dumps()`, this mocked method requires no arguments. In fact, it will accept any arguments that you pass to it.
- The return value of `dumps()` is also a Mock.
  - The capability of Mock to recursively define other mocks allows for you to use mocks in complex situations:

    ``` py
    >>> json = Mock()
    >>> json.loads('{"k": "v"}').get('k')
    <Mock name='mock.loads().get()' id='4379599424'>
    ```

  - Since the return value of each mocked method is also a Mock, you can use your mocks in a multitude of ways.

### Assertions and Inspection

- Mock instances store data on how you used them i.e. you can see if you called a method, how you called the method, and so on.
- There are two main ways to use this information.
  - First, you can assert that your program used an object as you expected:

        ``` py
        >>> from unittest.mock import Mock

        >>> # Create a mock object
        >>> json = Mock()

        >>> json.loads('{"key": "value"}')
        <Mock name='mock.loads()' id='4550144184'>

        >>> # You know that you called loads() so you can
        >>> # make assertions to test that expectation
        >>> json.loads.assert_called()
        >>> json.loads.assert_called_once()
        >>> json.loads.assert_called_with('{"key": "value"}')
        >>> json.loads.assert_called_once_with('{"key": "value"}')

        >>> json.loads('{"key": "value"}')
        <Mock name='mock.loads()' id='4550144184'>

        >>> # If an assertion fails, the mock will raise an AssertionError
        >>> json.loads.assert_called_once()
        Traceback (most recent call last):
        File "<stdin>", line 1, in <module>
        File "/usr/local/Cellar/python/3.6.5/Frameworks/Python.framework/Versions/3.6/lib/python3.6/unittest/mock.py", line 795, in assert_called_once
            raise AssertionError(msg)
        AssertionError: Expected 'loads' to have been called once. Called 2 times.

        >>> json.loads.assert_called_once_with('{"key": "value"}')
        Traceback (most recent call last):
        File "<stdin>", line 1, in <module>
        File "/usr/local/Cellar/python/3.6.5/Frameworks/Python.framework/Versions/3.6/lib/python3.6/unittest/mock.py", line 824, in assert_called_once_with
            raise AssertionError(msg)
        AssertionError: Expected 'loads' to be called once. Called 2 times.

        >>> json.loads.assert_not_called()
        Traceback (most recent call last):
        File "<stdin>", line 1, in <module>
        File "/usr/local/Cellar/python/3.6.5/Frameworks/Python.framework/Versions/3.6/lib/python3.6/unittest/mock.py", line 777, in assert_not_called
            raise AssertionError(msg)
        AssertionError: Expected 'loads' to not have been called. Called 2 times.
        ```

    - `.assert_called()` ensures you called the mocked method while `.assert_called_once()` checks that you called the method exactly one time.
    - Both assertion functions have variants that let you inspect the arguments passed to the mocked method:
      - `.assert_called_with(*args, **kwargs)`
      - `.assert_called_once_with(*args, **kwargs)`
    - To pass these assertions, you must call the mocked method with the same arguments that you pass to the actual method:

            ``` py
            >>> json = Mock()
            >>> json.loads(s='{"key": "value"}')
            >>> json.loads.assert_called_with('{"key": "value"}')
            Traceback (most recent call last):
            File "<stdin>", line 1, in <module>
            File "/usr/local/Cellar/python/3.6.5/Frameworks/Python.framework/Versions/3.6/lib/python3.6/unittest/mock.py", line 814, in assert_called_with
                raise AssertionError(_error_message()) from cause
            AssertionError: Expected call: loads('{"key": "value"}')
            Actual call: loads(s='{"key": "value"}')
            >>> json.loads.assert_called_with(s='{"key": "value"}')
            json.loads.assert_called_with('{"key": "value"}') raised an AssertionError because it expected you to call loads() with a positional argument, but you actually called it with a keyword argument. json.loads.assert_called_with(s='{"key": "value"}') gets this assertion correct.
            ```

  - Second, you can view special attributes to understand how your application used an object:

        ``` py
        >>> from unittest.mock import Mock

        >>> # Create a mock object
        >>> json = Mock()
        >>> json.loads('{"key": "value"}')
        <Mock name='mock.loads()' id='4391026640'>

        >>> # Number of times you called loads():
        >>> json.loads.call_count
        1
        >>> # The last loads() call:
        >>> json.loads.call_args
        call('{"key": "value"}')
        >>> # List of loads() calls:
        >>> json.loads.call_args_list
        [call('{"key": "value"}')]
        >>> # List of calls to json's methods (recursively):
        >>> json.method_calls
        [call.loads('{"key": "value"}')]
        ```

    - You can write tests using these attributes to make sure that your objects behave as you intended.

### Managing a Mock’s Return Value

- One reason to use mocks is to control your code’s behavior during tests. One way to do this is to specify a function’s return value.

    ``` py
    # my_calendar.py
    from datetime import datetime

    def is_weekday():
        today = datetime.today()
        # Python's datetime library treats Monday as 0 and Sunday as 6
        return (0 <= today.weekday() < 5)

    # Test if today is a weekday
    assert is_weekday()
    ```

- Since you’re testing if today is a weekday, the result depends on the day you run your test:

    ``` sh
    $ python my_calendar.py

    # If this command produces no output, the assertion was successful. 
    # Unfortunately, if you run the command on a weekend, you’ll get an AssertionError

    $ python my_calendar.py
    Traceback (most recent call last):
    File "test.py", line 9, in <module>
        assert is_weekday()
    AssertionError
    ```

- When writing tests, it is important to ensure that the results are predictable.
  - You can use Mock to eliminate uncertainty from your code during testing.
  - In this case, you can mock datetime and set the `.return_value` for `.today()` to a day that you choose:

    ``` py
    import datetime
    from unittest.mock import Mock

    # Save a couple of test days
    tuesday = datetime.datetime(year=2019, month=1, day=1)
    saturday = datetime.datetime(year=2019, month=1, day=5)

    # Mock datetime to control today's date
    datetime = Mock()

    def is_weekday():
        today = datetime.datetime.today()
        # Python's datetime library treats Monday as 0 and Sunday as 6
        return (0 <= today.weekday() < 5)

    # Mock .today() to return Tuesday
    datetime.datetime.today.return_value = tuesday
    # Test Tuesday is a weekday
    assert is_weekday()
    # Mock .today() to return Saturday
    datetime.datetime.today.return_value = saturday
    # Test Saturday is not a weekday
    assert not is_weekday()
    ```

  - In the example, `.today()` is a mocked method.
  - You’ve removed the inconsistency by assigning a specific day to the mock’s `.return_value`. That way, when you call `.today()`, it returns the datetime that you specified.
    - In the first test, you ensure tuesday is a weekday.
    - In the second test, you verify that saturday is not a weekday.
    - Now, it doesn’t matter what day you run your tests on because you’ve mocked datetime and have control over the object’s behavior.
- When building your tests, you will likely come across cases where mocking a function’s return value will not be enough. This is because functions are often more complicated than a simple one-way flow of logic.

### Managing a Mock’s Side Effects

- You can control your code’s behavior by specifying a mocked function’s side effects. A .side_effect defines what happens when you call the mocked function.

    ``` py
    # my_calendar.py:
    import requests

    # makes a request to the localhost server for a set of holidays. 
    # If the server responds successfully, get_holidays() will return a dictionary. 
    # Otherwise, the method will return None.
    def get_holidays():
        r = requests.get('http://localhost/api/holidays')
        if r.status_code == 200:
            return r.json()
        return None
    ```

- You can test how get_holidays() will respond to a connection timeout by setting requests.get.side_effect.

    ``` py
    import unittest
    from requests.exceptions import Timeout
    from unittest.mock import Mock

    # Mock requests to control its behavior
    requests = Mock()

    def get_holidays():
        r = requests.get('http://localhost/api/holidays')
        if r.status_code == 200:
            return r.json()
        return None

    class TestCalendar(unittest.TestCase):
        def test_get_holidays_timeout(self):
            # Test a connection timeout
            requests.get.side_effect = Timeout
            # verify that get_holidays() raises an exception given the new side effect of get().
            with self.assertRaises(Timeout):   
                get_holidays()

    if __name__ == '__main__':
        unittest.main()
    ```

- Run this test to see the result of your test:

    ``` sh
    $ python my_calendar.py
    .
    -------------------------------------------------------
    Ran 1 test in 0.000s

    OK
    ```

If you want to be a little more dynamic, you can set .side_effect to a function that Mock will invoke when you call your mocked method. The mock shares the arguments and return value of the .side_effect function:

``` py
import requests
import unittest
from unittest.mock import Mock

# Mock requests to control its behavior
requests = Mock()

def get_holidays():
    r = requests.get('http://localhost/api/holidays')
    if r.status_code == 200:
        return r.json()
    return None

class TestCalendar(unittest.TestCase):
    def log_request(self, url):
        # Log a fake request for test output purposes
        print(f'Making a request to {url}.')
        print('Request received!')

        # Create a new Mock to imitate a Response
        response_mock = Mock()
        response_mock.status_code = 200
        response_mock.json.return_value = {
            '12/25': 'Christmas',
            '7/4': 'Independence Day',
        }
        return response_mock

    def test_get_holidays_logging(self):
        # Test a successful, logged request
        requests.get.side_effect = self.log_request
        assert get_holidays()['12/25'] == 'Christmas'

if __name__ == '__main__':
    unittest.main()
```

First, you created .log_request(), which takes a URL, logs some output using print(), then returns a Mock response. Next, you set the .side_effect of get() to .log_request(), which you’ll use when you call get_holidays(). When you run your test, you’ll see that get() forwards its arguments to .log_request() then accepts the return value and returns it as well:

``` sh
$ python my_calendar.py
Making a request to http://localhost/api/holidays.
Request received!
.
-------------------------------------------------------
Ran 1 test in 0.000s

OK
```

Great! The print() statements logged the correct values. Also, get_holidays() returned the holidays dictionary.

.side_effect can also be an iterable. The iterable must consist of return values, exceptions, or a mixture of both. The iterable will produce its next value every time you call your mocked method. For example, you can test that a retry after a Timeout returns a successful response:

``` py
import unittest
from requests.exceptions import Timeout
from unittest.mock import Mock

# Mock requests to control its behavior
requests = Mock()

def get_holidays():
    r = requests.get('http://localhost/api/holidays')
    if r.status_code == 200:
        return r.json()
    return None

class TestCalendar(unittest.TestCase):
    def test_get_holidays_retry(self):
        # Create a new Mock to imitate a Response
        response_mock = Mock()
        response_mock.status_code = 200
        response_mock.json.return_value = {
            '12/25': 'Christmas',
            '7/4': 'Independence Day',
        }
        # Set the side effect of .get()
        requests.get.side_effect = [Timeout, response_mock]
        # Test that the first request raises a Timeout
        with self.assertRaises(Timeout):
            get_holidays()
        # Now retry, expecting a successful response
        assert get_holidays()['12/25'] == 'Christmas'
        # Finally, assert .get() was called twice
        assert requests.get.call_count == 2

if __name__ == '__main__':
    unittest.main()
```

The first time you call get_holidays(), get() raises a Timeout. The second time, the method returns a valid holidays dictionary. These side effects match the order they appear in the list passed to .side_effect.

You can set .return_value and .side_effect on a Mock directly. However, because a Python mock object needs to be flexible in creating its attributes, there is a better way to configure these and other settings.

### Configuring Your Mock

You can configure a Mock to set up some of the object’s behaviors. Some configurable members include .side_effect, .return_value, and .name. You configure a Mock when you create one or when you use .configure_mock().

You can configure a Mock by specifying certain attributes when you initialize an object:

``` py
>>> mock = Mock(side_effect=Exception)
>>> mock()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/Cellar/python/3.6.5/Frameworks/Python.framework/Versions/3.6/lib/python3.6/unittest/mock.py", line 939, in __call__
    return _mock_self._mock_call(*args, **kwargs)
  File "/usr/local/Cellar/python/3.6.5/Frameworks/Python.framework/Versions/3.6/lib/python3.6/unittest/mock.py", line 995, in _mock_call
    raise effect
Exception
>>> mock = Mock(name='Real Python Mock')
>>> mock
<Mock name='Real Python Mock' id='4434041432'>

>>> mock = Mock(return_value=True)
>>> mock()
True
```

While .side_effect and .return_value can be set on the Mock instance, itself, other attributes like .name can only be set through .__init__() or .configure_mock(). If you try to set the .name of the Mock on the instance, you will get a different result:

``` py
>>> mock = Mock(name='Real Python Mock')
>>> mock.name
<Mock name='Real Python Mock.name' id='4434041544'>

>>> mock = Mock()
>>> mock.name = 'Real Python Mock'
>>> mock.name
'Real Python Mock'
```

.name is a common attribute for objects to use. So, Mock doesn’t let you set that value on the instance in the same way you can with .return_value or .side_effect. If you access mock.name you will create a .name attribute instead of configuring your mock.

You can configure an existing Mock using .configure_mock():

``` py
>>> mock = Mock()
>>> mock.configure_mock(return_value=True)
>>> mock()
True
```

By unpacking a dictionary into either .configure_mock() or Mock.__init__(), you can even configure your Python mock object’s attributes. Using Mock configurations, you could simplify a previous example:

``` py
# Verbose, old Mock
response_mock = Mock()
response_mock.json.return_value = {
    '12/25': 'Christmas',
    '7/4': 'Independence Day',
}

# Shiny, new .configure_mock()
holidays = {'12/25': 'Christmas', '7/4': 'Independence Day'}
response_mock = Mock(**{'json.return_value': holidays})
```

Now, you can create and configure Python mock objects. You can also use mocks to control the behavior of your application. So far, you’ve used mocks as arguments to functions or patching objects in the same module as your tests.

## Using Mock

### Mock Patching Methods

- Common uses for Mock objects include:
  - Patching methods
  - Recording method calls on objects

#### Testing if an object was called correctly

- You might want to replace a method on an object to check that it is called with the correct arguments by another part of the system:

    ```py
    real = SomeClass()
    real.method = MagicMock(name='method')
    real.method(3, 4, 5, key='value')
    >>> <MagicMock name='method()' id='...'>
    ```

  - Once the mock method has been used (real.method in this example) it has methods and attributes that allow you to make assertions about how it has been used.
  - Once the mock has been called, its called attribute is set to True.
  - Can use the `assert_called_with()` or `assert_called_once_with()` method to check that it was called with the correct arguments.
        <br/>

> Note: In most of these examples the `Mock` and `MagicMock` classes are interchangeable. As the MagicMock is the more capable class it makes a sensible one to use by default.

### Mock for Method Calls on an Object

- Another common use case is to pass an object into a method (or some part of the system under test) and then check that it is used in the correct way.

    ```py
    # Test if calling ProductionClass().method() results in a call to the something() method:
    class ProductionClass:
        def method(self):
            self.something(1, 2, 3)
        def something(self, a, b, c):
            pass

    real = ProductionClass()
    real.something = MagicMock()
    real.method()
    real.something.assert_called_once_with(1, 2, 3)
    ```

- The simple ProductionClass below has a closer method. If it is called with an object then it calls close on it.

    ```py
    class ProductionClass:
        def closer(self, something):
            something.close()
    ```

  - To test it, we need to pass in an object with a `close` method and check that it was called correctly.

        ```py
        real = ProductionClass()
        mock = Mock()
        real.closer(mock)
        mock.close.assert_called_with()
        ```

    - No need to do any work to provide the `close` method on our mock. Accessing `close` creates it.
    - if `close` hasn’t already been called then accessing it in the test will create it, but assert_called_with() will raise a failure exception.

### Mocking Classes

- A common use case is to mock out classes instantiated by your code under test.
- When you patch a class, then that class is replaced with a mock.
- Instances are created by calling the class. This means you access the “mock instance” by looking at the return value of the mocked class.

    ``` py

    # some_function() creates an instance of `Foo` and calls a method on it.
    def some_function():
        instance = module.Foo()
        return instance.method()

    # The call to patch() replaces the class Foo with a mock.
    # The Foo instance is the result of calling the mock, so it is configured by modifying the mock return_value.
    with patch('module.Foo') as mock:
        # re-defining some_function's behaviour by replacing module.Foo() with mock
        instance = mock.return_value  # i.e. output when doing a fake module.Foo()
        instance.method.return_value = 'the result'

        # actual testing with mock
        result = some_function()
        assert result == 'the result'
    ```

### Naming your mocks

- The name is shown in the repr of the mock and can be helpful when the mock appears in test failure messages.
- The name is also propagated to attributes or methods of the mock:

    ``` py
    mock = MagicMock(name='foo')
    mock
    >>> <MagicMock name='foo' id='...'>
    mock.method
    >>> <MagicMock name='foo.method' id='...'>
    ```

### Tracking all Calls

- Often you want to track more than a single call to a method.
- The `mock_calls` attribute records all calls to child attributes of the mock and also to their children.

    ``` py
    mock = MagicMock()
    mock.method()
    >>> <MagicMock name='mock.method()' id='...'>
    mock.attribute.method(10, x=53)
    >>> <MagicMock name='mock.attribute.method()' id='...'>
    mock.mock_calls
    >>> [call.method(), call.attribute.method(10, x=53)]
    ```

  - If you make an assertion about `mock_calls` and any unexpected methods have been called, then the assertion will fail.
  - This is useful because as well as asserting that the calls you expected have been made, you are also checking that they were made in the right order and with no additional calls:

- You use the `call` object to construct lists for comparing with `mock_calls`:

    ``` py
    expected = [call.method(), call.attribute.method(10, x=53)]
    mock.mock_calls == expected
    >>> True
    ```

- However, parameters to calls that return mocks are not recorded, which means it is not possible to track nested calls where the parameters used to create ancestors are important:

    ``` py
    m = Mock()
    m.factory(important=True).deliver()
    >>> <Mock name='mock.factory().deliver()' id='...'>
    m.mock_calls[-1] == call.factory(important=False).deliver()
    >>> True
    ```

### Setting Return Values and Attributes

- Setting the return values on a mock object is trivially easy:

    ``` py
    mock = Mock()
    mock.return_value = 3
    mock()
    >>> 3
    ```

- Of course you can do the same for methods on the mock:

    ``` py
    mock = Mock()
    mock.method.return_value = 3
    mock.method()
    >>> 3
    ```

- The return value can also be set in the constructor:

    ``` py
    mock = Mock(return_value=3)
    mock()
    >>> 3
    ```

- If you need an attribute setting on your mock, just do it:

    ``` py
    mock = Mock()
    mock.x = 3
    mock.x
    >>> 3
    ```

- To mock up a more complex situation for e.g. `mock.connection.cursor().execute("SELECT 1")`. If we wanted this call to return a list, then we have to configure the result of the nested call.

    ``` py
    # We can use call to construct the set of calls in a “chained call” like this for easy assertion afterwards:
    mock = Mock()
    cursor = mock.connection.cursor.return_value
    cursor.execute.return_value = ['foo']
    mock.connection.cursor().execute("SELECT 1")
    >>> ['foo']
    
    # the call to .call_list() turns our call object into a list of calls representing the chained calls.
    expected = call.connection.cursor().execute("SELECT 1").call_list()
    mock.mock_calls
    >>> [call.connection.cursor(), call.connection.cursor().execute('SELECT 1')]
    mock.mock_calls == expected
    >>> True
    ```

### Raising exceptions with mocks

- A useful attribute is side_effect. If you set this to an exception class or instance then the exception will be raised when the mock is called.

    ``` py
    mock = Mock(side_effect=Exception('Boom!'))
    mock()
    >>> Traceback (most recent call last):
    >>> ...
    >>> Exception: Boom!
    ```

### Creating a Mock from an Existing Object

- One problem with over use of mocking is that it couples your tests to the implementation of your mocks rather than your real code.
  - Suppose you have a class that implements `some_method`.
  - In a test for another class, you provide a mock of this object that also provides `some_method`.
  - If later you refactor the first class, so that it no longer has `some_method` then your tests will continue to pass even though your code is now broken!

- Mock allows you to provide an object as a specification for the mock, using the `spec` keyword argument.
  - Accessing methods / attributes on the mock that don’t exist on your specification object will immediately raise an attribute error.
  - If you change the implementation of your specification, then tests that use that class will start failing immediately without you having to instantiate the class in those tests.

    ``` py
    mock = Mock(spec=SomeClass) # Create a mock while having SomeClass define it
    mock.old_method()
    >>> Traceback (most recent call last):
    >>> ...
    >>> AttributeError: object has no attribute 'old_method'
    ```

- Using a specification also enables a smarter matching of calls made to the mock, regardless of whether some parameters were passed as positional or named arguments:

    ``` py
    def f(a, b, c): pass

    mock = Mock(spec=f)
    mock(1, 2, 3)
    >>> <Mock name='mock()' id='140161580456576'>
    mock.assert_called_with(a=1, b=2, c=3)
    ```

- If you want this smarter matching to also work with method calls on the mock, you can use `auto-speccing`.
- If you want a stronger form of specification that prevents the setting of arbitrary attributes as well as the getting of them then you can use `spec_set` instead of `spec`.
