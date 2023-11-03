---
id: 6dogn3hd0am59e7q4o5tqdl
title: fixtures
desc: ''
updated: 1698627101274
created: 1698427330354
---

## Fixture

### What is a Fixture

- Fixtures are functions that are run by pytest before (and sometimes after) the actual test functions.
- The code in the fixture can do whatever you want it to.
  - You can use fixtures to get a data set for the tests to work on.
  - You can use fixtures to get a system into a known state before running a test.
- Fixtures are also used to get data ready for multiple tests.
- Fixtures are defined using the `@pytest.fixture` decorator and can be parameterized to accept arguments that are passed in from tests.

``` py
import​ ​pytest​
​ 
​ 
​ @pytest.fixture()   # indicate curr function is a fixture
​ ​def​ ​some_data​():
​     ​"""Return answer to ultimate question."""​
​     ​return​ 42
​ 
​ 
# When a fixture name is used as param of a test function, pytest will run it before running the test. 
​ ​def​ ​test_some_data​(some_data):
​     ​"""Use fixture return value in a test."""​
​     ​assert​ some_data == 42
```

- The `test test_some_data()` has the name of the fixture, `some_data``, as a parameter.
- pytest will see this and look for a fixture with this name.
- pytest will look in the module of the test for a fixture of that name.
- It will also look in `conftest.py` files if it doesn’t find it in this file.

### Sharing Fixtures Through `conftest.py`

- The location of `conftest.py` will determine the accessibility to fixtures defined in it that tests will have
  - For fixtures in individual test files, only tests in that file can access those fixtures
  - For`conftest.py` files located in a folder, fixtures defined in these files will be available to tests in that folder and its child folders.
- For fixtures to be accessible to all tests, the fixtures need to be in a `conftest.py` file centrally located for all of the tests, at the test root folder
- Although `conftest.py` is a Python module, it **should not be imported by test files**.
  - The `conftest.py` file gets read by pytest, and is considered a local plugin

### Fixtures for Setup and Teardown

#### The `yield` keyword

- A fixture function runs before the tests that use it.
- However, if there is a `yield` in the function, it stops there, passes control to the tests, and picks up on the next line after the tests are done.
- Therefore, think of the code above the yield as “setup” and the code after yield as “teardown.”
- The code after the yield, the “teardown,” is guaranteed to run regardless of what happens during the tests.
- Note that the yield keyword can be used to return data as well

#### Example

- pytest includes a cool fixture called tmpdir that we can use for testing and don’t have to worry about cleaning up. See [[tmpdir]]

``` py
# /tests/conftest.py

​​import​ ​pytest​
​​import​ ​tasks​
​​from​ ​tasks​ ​import​ Task
​ 
​ 
​@pytest.fixture()
​​def​ ​tasks_db​(tmpdir):    # tmpdir is an object that represents a directory
​    ​"""Connect to db before tests, disconnect after."""​
​    ​# Setup : start db​
    # str() to convert tmpdir into string
​    tasks.start_tasks_db(str(tmpdir), ​'tiny'​)
​ 
​    ​yield​  ​# this is where the testing happens​
​ 
​    ​# Teardown : stop db​
​    tasks.stop_tasks_db()


# /tests/func/test_add.py

​ ​import​ ​pytest​
​ ​import​ ​tasks​
​ ​from​ ​tasks​ ​import​ Task
​ 
​ 
​ ​def​ ​test_add_returns_valid_id​(tasks_db):
​     ​"""tasks.add(<valid task>) should return an integer."""​
​     ​# GIVEN an initialized tasks db​
​     ​# WHEN a new task is added​
​     ​# THEN returned task_id is of type int​
​     new_task = Task(​'do something'​)
​     task_id = tasks.add(new_task)
​     ​assert​ isinstance(task_id, int)
```

### Tracing Fixture Execution with `–setup-show`

- Pytest provides a helpful feature with the --setup-show option that helps with see what’s running and when

``` sh
# default
​ ​$ ​​cd​​ ​​/path/to/code/​
​ ​$ ​​pip​​ ​​install​​ ​​./tasks_proj/​​  ​​# if not installed yet​
​ ​$ ​​cd​​ ​​/path/to/code/ch3/a/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add.py​​ ​​-k​​ ​​valid_id​
​ =================== test session starts ===================
​ collected 3 items / 2 deselected
​ 
​ test_add.py::test_add_returns_valid_id PASSED       [100%]
​ 
​ ========= 1 passed, 2 deselected in 0.04 seconds ==========

# using –setup-show option
​ ​$ ​​pytest​​ ​​--setup-show​​ ​​test_add.py​​ ​​-k​​ ​​valid_id​
​ =================== test session starts ===================
​ collected 3 items / 2 deselected
​
​ test_add.py
​ SETUP    S tmpdir_factory
​         SETUP    F tmpdir (fixtures used: tmpdir_factory)
​         SETUP    F tasks_db (fixtures used: tmpdir)
​         func/test_add.py::test_add_returns_valid_id
​         (fixtures used: tasks_db, tmpdir, tmpdir_factory).
​         TEARDOWN F tasks_db
​         TEARDOWN F tmpdir
​ TEARDOWN S tmpdir_factory
​
​ ========= 1 passed, 2 deselected in 0.03 seconds ==========
```

- Notice the test is in the middle
- pytest designates a SETUP and TEARDOWN portion to each fixture.
- Going from `test_add_returns_valid_id`` up, you see that tmpdir ran before the test. And before that, tmpdir_factory. Apparently, tmpdir uses it as a fixture.
- The F and S in front of the fixture names indicate scope.
  - F for function scope
  - S for session scope.

### Fixtures for Storing Test Data

- Fixtures are a great place to store data to use for testing.
- You can return anything.

``` py
# Example of fixture returning a tuple of mixed type:
# /test_fixtures.py

​ @pytest.fixture()
​ ​def​ ​a_tuple​():
​     ​"""Return something more interesting."""​
​     ​return​ (1, ​'foo'​, None, {​'bar'​: 23})
​ 
​ 
​ ​def​ ​test_a_tuple​(a_tuple):
​     ​"""Demo the a_tuple fixture."""​
​     ​assert​ a_tuple[3][​'bar'​] == 32
```

#### When a test with a fixture fails

- Along with the stack trace section, pytest reports the value parameters of the function that raised the exception or failed an assert.
- In the case of tests, the fixtures are parameters to the test, and are therefore reported with the stack trace.

``` sh
# Here's what happens when a test with a fixture fails:(23 != 32)
​ ​$ ​​cd​​ ​​/path/to/code/ch3​
​ ​$ ​​pytest​​ ​​test_fixtures.py::test_a_tuple​
​ =================== test session starts ===================
​ collected 1 item
​ 
​ test_fixtures.py F                                  [100%]
​ 
​ ======================== FAILURES =========================
​ ______________________ test_a_tuple _______________________
​ 
​ a_tuple = (1, 'foo', None, {'bar': 23})
​ 
​     def test_a_tuple(a_tuple):
​         """Demo the a_tuple fixture."""
​ ​>​​       ​​assert​​ ​​a_tuple[3][​​'bar'​​]​​ ​​==​​ ​​32​
​ E       assert 23 == 32
​ 
​ test_fixtures.py:43: AssertionError
​ ================ 1 failed in 0.07 seconds =================
```

#### What happens if the assert (or any exception) happens in the fixture?

- A couple of things happen.
  - The stack trace shows correctly that the assert happened in the fixture function.
  - test_other_data is reported not as FAIL, but as ERROR. This distinction is great. If a test ever fails, you know the failure happened in the test proper, and not in any fixture it depends on.

``` sh
​ ​$ ​​pytest​​ ​​-v​​ ​​test_fixtures.py::test_other_data​
​ =================== test session starts ===================
​ collected 1 item
​
​ test_fixtures.py::test_other_data ERROR             [100%]
​
​ ========================= ERRORS ==========================
​ ____________ ERROR at setup of test_other_data ____________
​
​     @pytest.fixture()
​     def some_other_data():
​         """Raise an exception from fixture."""
​         x = 43
​ ​>​​       ​​assert​​ ​​x​​ ​​==​​ ​​42​
​ E       assert 43 == 42
​
​ test_fixtures.py:24: AssertionError
​ ================= 1 error in 0.06 seconds =================
```

But what about the Tasks project? For the Tasks project, we could probably use some data fixtures, perhaps different lists of tasks with various properties:

- You can use these directly from tests, or you can use them from other fixtures.

``` py
# /tests/conftest.py

​ ​# Reminder of Task constructor interface​
​ ​# Task(summary=None, owner=None, done=False, id=None)​
​ ​# summary is required​
​ ​# owner and done are optional​
​ ​# id is set by database​
​
​ @pytest.fixture()
​ ​def​ ​tasks_just_a_few​():
​     ​"""All summaries and owners are unique."""​
​     ​return​ (
​         Task(​'Write some code'​, ​'Brian'​, True),
​         Task(​"Code review Brian's code"​, ​'Katie'​, False),
​         Task(​'Fix what Brian did'​, ​'Michelle'​, False))
​
​
​ @pytest.fixture()
​ ​def​ ​tasks_mult_per_owner​():
​     ​"""Several owners with several tasks each."""​
​     ​return​ (
​         Task(​'Make a cookie'​, ​'Raphael'​),
​         Task(​'Use an emoji'​, ​'Raphael'​),
​         Task(​'Move to Berlin'​, ​'Raphael'​),
​
​         Task(​'Create'​, ​'Michelle'​),
​         Task(​'Inspire'​, ​'Michelle'​),
​         Task(​'Encourage'​, ​'Michelle'​),
​
​         Task(​'Do a handstand'​, ​'Daniel'​),
​         Task(​'Write some books'​, ​'Daniel'​),
​         Task(​'Eat ice cream'​, ​'Daniel'​))
```

You’ve already seen that tmpdir uses tmpdir_factory. And you used tmpdir in our tasks_db fixture. Let’s keep the chain going and add some specialized fixtures for non-empty tasks databases:

### Using Multiple Fixtures

- Notice that these fixtures all include two fixtures each in their parameter list: tasks_db and a data set.
- The data set is used to add tasks to the database.
- Now tests can use these when you want the test to start from a non-empty database, like this:

``` py
# /tests/conftest.py

​ @pytest.fixture()
​ ​def​ ​db_with_3_tasks​(tasks_db, tasks_just_a_few):
​     ​"""Connected db with 3 tasks, all unique."""​
​     ​for​ t ​in​ tasks_just_a_few:
​         tasks.add(t)
​ 
​ 
​ @pytest.fixture()
​ ​def​ ​db_with_multi_per_owner​(tasks_db, tasks_mult_per_owner):
​     ​"""Connected db with 9 tasks, 3 owners, all with 3 tasks."""​
​     ​for​ t ​in​ tasks_mult_per_owner:
​         tasks.add(t)

# /tests/func/test_add.py

​ ​def​ ​test_add_increases_count​(db_with_3_tasks):
​     ​"""Test tasks.add() affect on tasks.count()."""​
​     ​# GIVEN a db with 3 tasks​
​     ​#  WHEN another task is added​
​     tasks.add(Task(​'throw a party'​))
​
​     ​#  THEN the count increases by 1​
​     ​assert​ tasks.count() == 4
```

- This also demonstrates one of the great reasons to use fixtures: to focus the test on what you’re actually testing, not on what you had to do to get ready for the test.
- I like using comments for GIVEN/WHEN/THEN and trying to push as much GIVEN into fixtures for two reasons.
  - First, it makes the test more readable and, therefore, more maintainable.
  - Second, an assert or exception in the fixture results in an ERROR, while an assert or exception in a test function results in a FAIL.
    - I don’t want test_add_increases_count() to FAIL if database initialization failed. That would just be confusing.
    - I want a FAIL for test_add_increases_count() to only be possible if add() really failed to alter the count.

```sh
​ ​$ ​​cd​​ ​​/path/to/code/ch3/a/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​--setup-show​​ ​​test_add.py::test_add_increases_count​
​ =================== test session starts ===================
​ collected 1 item
​
​ test_add.py
​ SETUP    S tmpdir_factory
​         SETUP    F tmpdir (fixtures used: tmpdir_factory)
​         SETUP    F tasks_db (fixtures used: tmpdir)
​         SETUP    F tasks_just_a_few
​         SETUP    F db_with_3_tasks (fixtures used: tasks_db, tasks_just_a_few)
​         test_add.py::test_add_increases_count (fixtures used: db_with_3_tasks,
​         tasks_db, tasks_just_a_few, tmpdir, tmpdir_factory).
​         TEARDOWN F db_with_3_tasks
​         TEARDOWN F tasks_just_a_few
​         TEARDOWN F tasks_db
​         TEARDOWN F tmpdir
​ TEARDOWN S tmpdir_factory
​
​ ================ 1 passed in 0.05 seconds =================
```

### Fixture scope

- Fixtures include an optional parameter called scope, which controls how often a fixture gets set up and torn down.
- The scope parameter to `@pytest.fixture()` can have the values of function, class, module, or session.
- The default scope is function. Fixtures with unspecified scopes are function scope fixtures.
- Fixtures can only depend on other fixtures of their same scope or wider.
  - So a function scope fixture can depend on other function scope fixtures (the default, and used in the Tasks project so far).
  - A function scope fixture can also depend on class, module, and session scope fixtures, but you can’t go in the reverse order.

#### List of available scopes

``` py
​ ​"""Demo fixture scope."""​
​ 
​ ​import​ ​pytest​
​ 
​ 
​ @pytest.fixture(scope=​'function'​)
​ ​def​ ​func_scope​():
​     ​"""A function scope fixture."""​
​ 
​ 
​ @pytest.fixture(scope=​'module'​)
​ ​def​ ​mod_scope​():
​     ​"""A module scope fixture."""​
​ 
​ 
​ @pytest.fixture(scope=​'session'​)
​ ​def​ ​sess_scope​():
​     ​"""A session scope fixture."""​
​ 
​ 
​ @pytest.fixture(scope=​'class'​)
​ ​def​ ​class_scope​():
​     ​"""A class scope fixture."""​
​ 
​ 
​ ​def​ ​test_1​(sess_scope, mod_scope, func_scope):
​     ​"""Test using session, module, and function scope fixtures."""​
​ 
​ 
​ ​def​ ​test_2​(sess_scope, mod_scope, func_scope):
​     ​"""Demo is more fun with multiple tests."""​
​ 
​ @pytest.mark.usefixtures(​'class_scope'​)
​ ​class​ TestSomething():
​     ​"""Demo class scope fixtures."""​
​ 
​     ​def​ ​test_3​(self):
​         ​"""Test using a class scope fixture."""​
​ 
​     ​def​ ​test_4​(self):
​         ​"""Again, multiple tests are more fun."""​
```

##### `scope=’function’`

Run once per test function. The setup portion is run before each test using the fixture. The teardown portion is run after each test using the fixture. This is the default scope used when no scope parameter is specified.

##### `scope=’class’`

Run once per test class, regardless of how many test methods are in the class.

##### `scope=’module’`

Run once per module, regardless of how many test functions or methods, or other fixtures in the module use it.

##### `scope=’package’`

Run once per package, or test directory, regardless of how many test functions or methods, or other fixtures in the package use it.

##### `scope=’session’`

Run once per session. All test methods and functions using a fixture of session scope share one setup and teardown call.

- Using setup-show to demonstrate that the number of times a fixture is called and when the setup and teardown are run depend on the scope:

``` sh
# Notice that you get to see not just F and S for function and session, 
# but also C and M for class and module.
​ 
 ​$ ​​cd​​ ​​/path/to/code/ch3​
​ ​$ ​​pytest​​ ​​--setup-show​​ ​​test_scope.py​
​ =================== test session starts ===================
​ collected 4 items
​ 
​ test_scope.py
​ SETUP    S sess_scope
​     SETUP    M mod_scope
​         SETUP    F func_scope
​         test_scope.py::test_1 (fixtures used: func_scope, mod_scope, sess_scope).
​         TEARDOWN F func_scope
​         SETUP    F func_scope
​         test_scope.py::test_2 (fixtures used: func_scope, mod_scope, sess_scope).
​         TEARDOWN F func_scope
​       SETUP    C class_scope
​         test_scope.py::TestSomething::()::test_3 (fixtures used: class_scope).
​         test_scope.py::TestSomething::()::test_4 (fixtures used: class_scope).
​       TEARDOWN C class_scope
​     TEARDOWN M mod_scope
​ TEARDOWN S sess_scope
​ 
​ ================ 4 passed in 0.02 seconds =================
```

#### Changing Scope for Fixtures

- To have something like tasks_db be session scope, you need to use tmpdir_factory, since tmpdir is function scope and tmpdir_factory is session scope.
- Luckily, this is just a one-line code change (well, two if you count tmpdir -> tmpdir_factory in the parameter list):

``` py
# /tests/conftest.py

​ ​import​ ​pytest​
​ ​import​ ​tasks​
​ ​from​ ​tasks​ ​import​ Task
​
​
# The data fixtures just return a value, so there really is no reason to have them run all the time. Once per session is sufficient:
​ @pytest.fixture(scope=​'session'​)
​ ​def​ ​tasks_db_session​(tmpdir_factory):
​     ​"""Connect to db before tests, disconnect after."""​
​     temp_dir = tmpdir_factory.mktemp(​'temp'​)
​     tasks.start_tasks_db(str(temp_dir), ​'tiny'​)
​     ​yield​
​     tasks.stop_tasks_db()
​
​
# changed tasks_db to depend on tasks_db_session
# deleted all the entries to make sure it’s empty. 
​ @pytest.fixture()
​ ​def​ ​tasks_db​(tasks_db_session):
​     ​"""An empty tasks db."""​
​     tasks.delete_all()


# /tests/conftest.py

​ ​# Reminder of Task constructor interface​
​ ​# Task(summary=None, owner=None, done=False, id=None)​
​ ​# summary is required​
​ ​# owner and done are optional​
​ ​# id is set by database​
​ @pytest.fixture(scope=​'session'​)
​ ​def​ ​tasks_just_a_few​():
​     ​"""All summaries and owners are unique."""​
​     ​return​ (
​         Task(​'Write some code'​, ​'Brian'​, True),
​         Task(​"Code review Brian's code"​, ​'Katie'​, False),
​         Task(​'Fix what Brian did'​, ​'Michelle'​, False))
​
​
​ @pytest.fixture(scope=​'session'​)
​ ​def​ ​tasks_mult_per_owner​():
​     ​"""Several owners with several tasks each."""​
​     ​return​ (
​         Task(​'Make a cookie'​, ​'Raphael'​),
​         Task(​'Use an emoji'​, ​'Raphael'​),
​         Task(​'Move to Berlin'​, ​'Raphael'​),
​
​         Task(​'Create'​, ​'Michelle'​),
​         Task(​'Inspire'​, ​'Michelle'​),
​         Task(​'Encourage'​, ​'Michelle'​),
​
​         Task(​'Do a handstand'​, ​'Daniel'​),
​         Task(​'Write some books'​, ​'Daniel'​),
​         Task(​'Eat ice cream'​, ​'Daniel'​))
```

Now, let’s see if all of these changes work with our tests:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch3/b/tasks_proj​
​ ​$ ​​pytest​
​ =================== test session starts ===================
​ collected 55 items
​
​ tests/func/test_add.py ...                          [  5%]
​ tests/func/test_add_variety.py .................... [ 41%]
​ ........                                            [ 56%]
​ tests/func/test_add_variety2.py ............        [ 78%]
​ tests/func/test_api_exceptions.py .......           [ 90%]
​ tests/func/test_unique_id.py .                      [ 92%]
​ tests/unit/test_task.py ....                        [100%]
​
​ ================ 55 passed in 0.33 seconds ================


​ ​$ ​​pytest​​ ​​--setup-show​​ ​​tests/func/test_add.py​
​ =================== test session starts ===================
​ collected 3 items
​
​ tests/func/test_add.py
​ SETUP    S tmpdir_factory
​ SETUP    S tasks_db_session (fixtures used: tmpdir_factory)
​         SETUP    F tasks_db (fixtures used: tasks_db_session)
​         func/test_add.py::test_add_returns_valid_id (fixtures used: tasks_db,
​         tasks_db_session, tmpdir_factory).
​         TEARDOWN F tasks_db
​         SETUP    F tasks_db (fixtures used: tasks_db_session)
​         func/test_add.py::test_added_task_has_id_set (fixtures used: tasks_db,
​         tasks_db_session, tmpdir_factory).
​         TEARDOWN F tasks_db
​ SETUP    S tasks_just_a_few
​         SETUP    F tasks_db (fixtures used: tasks_db_session)
​         SETUP    F db_with_3_tasks (fixtures used: tasks_db, tasks_just_a_few)
​         func/test_add.py::test_add_increases_count (fixtures used: db_with_3_tasks,
​         tasks_db, tasks_db_session, tasks_just_a_few, tmpdir_factory).
​         TEARDOWN F db_with_3_tasks
​         TEARDOWN F tasks_db
​ TEARDOWN S tasks_db_session
​ TEARDOWN S tmpdir_factory
​ TEARDOWN S tasks_just_a_few
​
​ ================ 3 passed in 0.04 seconds =================

# tasks_db_session is called once per session
# the quicker tasks_db now just cleans out the database before each test.
```

### Specifying Fixtures with usefixtures

- You can also mark a test or a class with @pytest.mark.usefixtures(’fixture1’, ’fixture2’).
- usefixtures takes a comma separated list of strings representing fixture names.
- It doesn’t make sense to do this with test functions — it’s just more typing. But it does work well for test classes:

``` py
​ @pytest.mark.usefixtures(​'class_scope'​)
​ ​class​ TestSomething():
​     ​"""Demo class scope fixtures."""​
​ 
​     ​def​ ​test_3​(self):
​         ​"""Test using a class scope fixture."""​
​ 
​     ​def​ ​test_4​(self):
​         ​"""Again, multiple tests are more fun."""​
```

- Using usefixtures is almost the same as specifying the fixture name in the test method parameter list.
- The one difference is that the test can use the return value of a fixture only if it’s specified in the parameter list.
- A test using a fixture due to usefixtures cannot use the fixture’s return value.

### Using autouse for Fixtures That Always Get Used

- use `autouse=True` to get a fixture to run all of the time.
- This works well for code you want to run at certain times, but tests don’t really depend on any system state or data from the fixture.
- Generally the autouse feature is good to have around. But it’s more of an exception than a rule. Opt for named fixtures unless you have a really great reason not to.

``` py
​"""Demonstrate autouse fixtures."""​
​ 
​ import​ ​pytest​
​ ​import​ ​time​
​ 
​ 
# the date and current time at the end of the session.
​ @pytest.fixture(autouse=True, scope=​'session'​)
​ ​def​ ​footer_session_scope​():
​     ​"""Report the time at the end of a session."""​
​     ​yield​
​     now = time.time()
​     ​print​(​'--'​)
​     ​print​(​'finished : {}'​.format(time.strftime(​'​​%​​d ​​%​​b ​​%​​X'​, time.localtime(now))))
​     ​print​(​'-----------------'​)
​ 
​ 
# add test times after each test
​ @pytest.fixture(autouse=True)
​ ​def​ ​footer_function_scope​():
​     ​"""Report test durations after each function."""​
​     start = time.time()
​     ​yield​
​     stop = time.time()
​     delta = stop - start
​     ​print​(​'​​\n​​test duration : {:0.3} seconds'​.format(delta))
​ 
​ 
​ ​def​ ​test_1​():
​     ​"""Simulate long-ish running test."""​
​     time.sleep(1)
​ 
​ 
​ ​def​ ​test_2​():
​     ​"""Simulate slightly longer test."""​
​     time.sleep(1.23)
```

```sh
​ ​$ ​​cd​​ ​​/path/to/code/ch3​
​ ​$ ​​pytest​​ ​​-v​​ ​​-s​​ ​​test_autouse.py​
​ ===================== test session starts ======================
​ collected 2 items
​ 
​ test_autouse.py::test_1 PASSED
​ test duration : 1.0 seconds
​ 
​ test_autouse.py::test_2 PASSED
​ test duration : 1.24 seconds
​ --
​ finished : 25 Jul 16:18:27
​ -----------------
​ =================== 2 passed in 2.25 seconds ===================
```

Now that you’ve seen autouse in action, you may be wondering why we didn’t use it for tasks_db in this chapter. In the Tasks project, I felt it was important to keep the ability to test what happens if we try to use an API function before db initialization. It should raise an appropriate exception. But we can’t test this if we force good initialization on every test.

### Renaming Fixtures

- The name of a fixture, listed in the parameter list of tests and other fixtures using it, is usually the same as the function name of the fixture.
- However, pytest allows you to rename fixtures with a name parameter to `@pytest.fixture()`

``` py
# test_rename_fixture.py
​ ​"""Demonstrate fixture renaming."""​
​
​ ​import​ ​pytest​
​
​
​ @pytest.fixture(name=​'lue'​)
​ ​def​ ​ultimate_answer_to_life_the_universe_and_everything​():
​     ​"""Return ultimate answer."""​
​     ​return​ 42
​
​
​ ​def​ ​test_everything​(lue):
​     ​"""Use the shorter name."""​
​     ​assert​ lue == 42
```

- Here, `lue` is now the fixture name, instead of `ultimate_answer_to_life_the_universe_and_everything`.
- That name even shows up if we run it with `— setup-show`:

``` sh
​ ​$ ​​pytest​​ ​​--setup-show​​ ​​test_rename_fixture.py​
​ =================== test session starts ===================
​ collected 1 item
​
​ test_rename_fixture.py
​         SETUP    F lue
​         test_rename_fixture.py::test_everything (fixtures used: lue).
​         TEARDOWN F lue
​
​ ================ 1 passed in 0.01 seconds =================
```

- To find out where the renamed fixture is defined, you can add the pytest option `—-fixtures` and give it the filename for the test.
- It lists all the fixtures available for the test, including ones that have been renamed:

``` sh
​ ​$ ​​pytest​​ ​​--fixtures​​ ​​test_rename_fixture.py​
​ =================== test session starts ===================
​ ​...​
​
​ -------- fixtures defined from test_rename_fixture --------
​ lue
​     Return ultimate answer.
​
​ ============== no tests ran in 0.01 seconds ===============
```

Cool. All of our conftest.py fixtures are there. And at the bottom of the builtin list is the tmpdir and tmpdir_factory that we used also.

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch3/b/tasks_proj​
​ ​$ ​​pytest​​ ​​--fixtures​​ ​​tests/func/test_add.py​
​ ======================== test session starts ========================
​ ​...​
​ tmpdir_factory
​     Return a TempdirFactory instance for the test session.
​ tmpdir
​     Return a temporary directory path object which is
​     unique to each test function invocation, created as
​     a sub directory of the base temporary directory.
​     The returned object is a `py.path.local`_path object.
​
​ ------------------ fixtures defined from conftest -------------------
​ tasks_db_session
​     Connect to db before tests, disconnect after.
​ tasks_db
​     An empty tasks db.
​ tasks_just_a_few
​     All summaries and owners are unique.
​ tasks_mult_per_owner
​     Several owners with several tasks each.
​ db_with_3_tasks
​     Connected db with 3 tasks, all unique.
​ db_with_multi_per_owner
​     Connected db with 9 tasks, 3 owners, all with 3 tasks.
​
​ =================== no tests ran in 0.01 seconds ====================
```

### Parametrizing Fixtures

- We can also parametrize fixtures.
- We still use our list of tasks, list of task identifiers, and an equivalence function, just as before:

``` py
#  /tests/func/test_add_variety2.py

​ ​import​ ​pytest​
​ ​import​ ​tasks​
​ ​from​ ​tasks​ ​import​ Task
​
​ tasks_to_try = (Task(​'sleep'​, done=True),
​                 Task(​'wake'​, ​'brian'​),
​                 Task(​'breathe'​, ​'BRIAN'​, True),
​                 Task(​'exercise'​, ​'BrIaN'​, False))
​
​ task_ids = [​'Task({},{},{})'​.format(t.summary, t.owner, t.done)
​             ​for​ t ​in​ tasks_to_try]
​
​
​ ​def​ ​equivalent​(t1, t2):
​     ​"""Check two tasks for equivalence."""​
​     ​return​ ((t1.summary == t2.summary) ​and​
​             (t1.owner == t2.owner) ​and​
​             (t1.done == t2.done))

# instead of parametrizing the test, we will parametrize a fixture called a_task:
# /tests/func/test_add_variety2.py


# Returns the request.param as its value to the test using it. 
# Since our task list has four tasks, the fixture will be called four times, and then the test will get called four times:
​ @pytest.fixture(params=tasks_to_try)
​ ​def​ ​a_task​(request):
​     ​"""Using no ids."""​
​     ​return​ request.param
​
​
​ ​def​ ​test_add_a​(tasks_db, a_task):
​     ​"""Using a_task fixture (no ids)."""​
​     task_id = tasks.add(a_task)
​     t_from_db = tasks.get(task_id)
​     ​assert​ equivalent(t_from_db, a_task)
```

- The request listed in the fixture parameter is another builtin fixture that represents the calling state of the fixture.
- It has a field param that is filled in with one element from the list assigned to params in `@pytest.fixture(params=tasks_to_try)`.

``` sh
# We didn’t provide ids, so pytest just made up some names by appending a number to the name of the fixture. 
​
 ​$ ​​cd​​ ​​/path/to/code/ch3/b/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety2.py::test_add_a​
​ =================== test session starts ===================
​ collected 4 items
​
​ test_add_variety2.py::test_add_a[a_task0] PASSED    [ 25%]
​ test_add_variety2.py::test_add_a[a_task1] PASSED    [ 50%]
​ test_add_variety2.py::test_add_a[a_task2] PASSED    [ 75%]
​ test_add_variety2.py::test_add_a[a_task3] PASSED    [100%]
​
​ ================ 4 passed in 0.05 seconds =================
```

##### Fixing the name issue

We can use the same string list we used when we parametrized our tests. This should gives us better identifiers

``` py
# /tests/func/test_add_variety2.py

​ @pytest.fixture(params=tasks_to_try, ids=task_ids)
​ ​def​ ​b_task​(request):
​     ​"""Using a list of ids."""​
​     ​return​ request.param
​
​
​ ​def​ ​test_add_b​(tasks_db, b_task):
​     ​"""Using b_task fixture, with ids."""​
​     task_id = tasks.add(b_task)
​     t_from_db = tasks.get(task_id)
​     ​assert​ equivalent(t_from_db, b_task)
```

``` sh
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety2.py::test_add_b​
​ =================== test session starts ===================
​ collected 4 items
​
​ test_add_variety2.py::test_add_b[Task(sleep,None,True)] PASSED [ 25%]
​ test_add_variety2.py::test_add_b[Task(wake,brian,False)] PASSED [ 50%]
​ test_add_variety2.py::test_add_b[Task(breathe,BRIAN,True)] PASSED [ 75%]
​ test_add_variety2.py::test_add_b[Task(exercise,BrIaN,False)] PASSED [100%]
​
​ ================ 4 passed in 0.04 seconds =================
```

<br>

##### Using a function to generate identifiers

- We can also set the ids parameter to a function we write that provides the identifiers.
- Here’s what it looks like when we use a function to generate the identifiers:

``` py
#  /tests/func/test_add_variety2.py

​ ​def​ ​id_func​(fixture_value):
​     ​"""A function for generating ids."""​
​     t = fixture_value
​     ​return​ ​'Task({},{},{})'​.format(t.summary, t.owner, t.done)
​
​
​ @pytest.fixture(params=tasks_to_try, ids=id_func)
​ ​def​ ​c_task​(request):
​     ​"""Using a function (id_func) to generate ids."""​
​     ​return​ request.param
​
​
​ ​def​ ​test_add_c​(tasks_db, c_task):
​     ​"""Use fixture with generated ids."""​
​     task_id = tasks.add(c_task)
​     t_from_db = tasks.get(task_id)
​     ​assert​ equivalent(t_from_db, c_task)
```

- The function will be called from the value of each item from the parametrization.
- Since the parametrization is a list of Task objects, id_func() will be called with a Task object, which allows us to use the namedtuple accessor methods to access a single Task object to generate the identifier for one Task object at a time.
- It’s a bit cleaner than generating a full list ahead of time, and looks the same:

``` sh
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety2.py::test_add_c​
​ =================== test session starts ===================
​ collected 4 items
​
​ test_add_variety2.py::test_add_c[Task(sleep,None,True)] PASSED [ 25%]
​ test_add_variety2.py::test_add_c[Task(wake,brian,False)] PASSED [ 50%]
​ test_add_variety2.py::test_add_c[Task(breathe,BRIAN,True)] PASSED [ 75%]
​ test_add_variety2.py::test_add_c[Task(exercise,BrIaN,False)] PASSED [100%]
​
​ ================ 4 passed in 0.05 seconds =================
```

With parametrized functions, you get to run that function multiple times. But with parametrized fixtures, every test function that uses that fixture will be called multiple times. Very powerful.
