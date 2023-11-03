---
id: wxzw4m0wdu7dq9i75qyl7dq
title: markers
desc: ''
updated: 1698634175329
created: 1698446153339
---

- [Marking](#marking)
  - [Skipping Test: `skip` and `skipif`](#skipping-test-skip-and-skipif)
    - [Skipping tests conditionally: `skipif()`](#skipping-tests-conditionally-skipif)
    - [Output when there's skipped tests](#output-when-theres-skipped-tests)
  - [Marking Tests as Expecting to Fail](#marking-tests-as-expecting-to-fail)
  - [Working with custom markers](#working-with-custom-markers)
    - [Running the custom tagged tests](#running-the-custom-tagged-tests)
    - [Combining multiple markers](#combining-multiple-markers)
  - [Parametrized Testing](#parametrized-testing)


## Marking

- pytest provides a cool mechanism to let you put markers on test functions.
- A test can have more than one marker, and a marker can be on multiple tests.
- Applied using `@pytest.mark.xxx`` decorator
- Allows for various extended functionality such as,
  - Grouping the unit tests: Multiple unit tests can then be run as a group
  - Marked to fail: To indicate that the unit test is expected to fail
  - Marked to skip/conditional skipping: Unit test default behaviour is to be skipped, or be skipped if certain conditions are met
  - Marked to insert parameters: Test various inputs to a unit test

### Skipping Test: `skip` and `skipif`

- The skip and skipif markers enable you to skip tests you don’t want to run.
- Marking a test to be skipped is as simple as adding @pytest.mark.skip() just above the test function.

**Example: Running tests but skipping `​test_unique_id_1​()`**

``` py
# /tests/func/test_unique_id_2.py

​ @pytest.mark.skip(reason=​'misunderstood the API'​)
​ ​def​ ​test_unique_id_1​():
​     ​"""Calling unique_id() twice should return different numbers."""​
​     id_1 = tasks.unique_id()
​     id_2 = tasks.unique_id()
​     ​assert​ id_1 != id_2
​ 
​ 
​ ​def​ ​test_unique_id_2​():
​     ​"""unique_id() should return an unused id."""​
​     ids = []
​     ids.append(tasks.add(Task(​'one'​)))
​     ids.append(tasks.add(Task(​'two'​)))
​     ids.append(tasks.add(Task(​'three'​)))
​     ​# grab a unique id​
​     uid = tasks.unique_id()
​     ​# make sure it isn't in the list of existing ids​
​     ​assert​ uid ​not​ ​in​ ids
```

``` sh
​ ​$ ​​pytest​​ ​​-v​​ ​​test_unique_id_2.py​
​ =================== test session starts ===================
​ collected 2 items
​
​ test_unique_id_2.py::test_unique_id_1 SKIPPED       [ 50%]
​ test_unique_id_2.py::test_unique_id_2 PASSED        [100%]
​
​ =========== 1 passed, 1 skipped in 0.03 seconds ===========
```

#### Skipping tests conditionally: `skipif()`

- Skipping a test conditionally can be done via `skipif()`
- The expression passed into `skipif()` can be any valid Python expression.
- Note that `reason` param is required in skipif.
- Anyways it's good practice to provide reason regardless for every skip, skipif, or xfail.

``` py
# /tests/func/test_unique_id_3.py

​ @pytest.mark.skipif(tasks.__version__ < ​'0.2.0'​,
​                     reason=​'not supported until version 0.2.0'​)
​ ​def​ ​test_unique_id_1​():
​     ​"""Calling unique_id() twice should return different numbers."""​
​     id_1 = tasks.unique_id()
​     id_2 = tasks.unique_id()
​     ​assert​ id_1 != id_2
```

#### Output when there's skipped tests

Here’s the output of the changed code:

``` sh
​ ​$ ​​pytest​​ ​​test_unique_id_3.py​
​ =================== test session starts ===================
​ collected 2 items
​
​ test_unique_id_3.py s.                              [100%]
​
​ =========== 1 passed, 1 skipped in 0.03 seconds ===========
```

- The `s.` shows that one test was skipped and one test passed.

We can see which one with `-v`:

``` sh
​ ​$ ​​pytest​​ ​​-v​​ ​​test_unique_id_3.py​
​ =================== test session starts ===================
​ collected 2 items
​
​ test_unique_id_3.py::test_unique_id_1 SKIPPED       [ 50%]
​ test_unique_id_3.py::test_unique_id_2 PASSED        [100%]
​
​ =========== 1 passed, 1 skipped in 0.03 seconds ===========
```

But we still don’t know why. We can see those reasons with -rs:

``` sh
​ ​$ ​​pytest​​ ​​-rs​​ ​​test_unique_id_3.py​
​ =================== test session starts ===================
​ collected 2 items
​
​ test_unique_id_3.py s.                              [100%]
​ ================= short test summary info =================
​ SKIP [1] test_unique_id_3.py:9: not supported until version 0.2.0
​
​ =========== 1 passed, 1 skipped in 0.04 seconds ===========
```

The -r chars option has this help text:

``` sh
​ ​$ ​​pytest​​ ​​--help​

​ ​...​
​   -r chars
​
​   show extra test summary info as specified by chars
​   (f)ailed, (E)error, (s)skipped, (x)failed, (X)passed,
​   (p)passed, (P)passed with output, (a)all except pP.
​ ​...​
```

It’s not only helpful for understanding test skips, but also you can use it for other test outcomes as well.

### Marking Tests as Expecting to Fail

- With the xfail marker, we are telling pytest to run a test function, but that we expect it to fail.

**Example**

- The first test is the same as before, but with xfail.
- The next two tests are listed as xfail, and differ only by == vs. !=. So one of them is bound to pass.

``` py
#  /tests/func/test_unique_id_4.py

​ @pytest.mark.xfail(tasks.__version__ < ​'0.2.0'​,
​                    reason=​'not supported until version 0.2.0'​)
​ ​def​ ​test_unique_id_1​():
​     ​"""Calling unique_id() twice should return different numbers."""​
​     id_1 = tasks.unique_id()
​     id_2 = tasks.unique_id()
​     ​assert​ id_1 != id_2
​
​
​ @pytest.mark.xfail()
​ ​def​ ​test_unique_id_is_a_duck​():
​     ​"""Demonstrate xfail."""​
​     uid = tasks.unique_id()
​     ​assert​ uid == ​'a duck'​
​
​
​ @pytest.mark.xfail()
​ ​def​ ​test_unique_id_not_a_duck​():
​     ​"""Demonstrate xpass."""​
​     uid = tasks.unique_id()
​     ​assert​ uid != ​'a duck'​
```

Running this shows:

``` sh

​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​test_unique_id_4.py​
​ =================== test session starts ===================
​ collected 4 items
​
​ test_unique_id_4.py xxX.                            [100%]
​
​ ===== 1 passed, 2 xfailed, 1 xpassed in 0.10 seconds ======
```

- The x is for XFAIL, which means “expected to fail.”
- The capital X is for XPASS or “expected to fail but passed.”

<br>

`— verbose` lists longer descriptions:

``` sh
​ ​$ ​​pytest​​ ​​-v​​ ​​test_unique_id_4.py​
​ =================== test session starts ===================
​ collected 4 items
​
​ test_unique_id_4.py::test_unique_id_1 xfail         [ 25%]
​ test_unique_id_4.py::test_unique_id_is_a_duck xfail [ 50%]
​ test_unique_id_4.py::test_unique_id_not_a_duck XPASS [ 75%]
​ test_unique_id_4.py::test_unique_id_2 PASSED        [100%]
​
​ ===== 1 passed, 2 xfailed, 1 xpassed in 0.10 seconds ======
```

You can configure pytest to report the tests that pass but were marked with xfail to be reported as FAIL. This is done in a pytest.ini file:

``` raw
​ [pytest]
​ xfail_strict=true
```

### Working with custom markers

- Smoke tests are used to quickly identify and fix any major issues with the software before more detailed testing is performed i.e. give a decent idea of the health of all parts of the system.
- pytest allows “mark” a test functions with 'tags' and then restrict a test run to run tests marked with those tags
- Also see [[lang.py.testing.pytest.config]] for registering markers to avoid marker typos

Suppose we are using the tag `smoke` to add tests into the smoke test suite

``` py
# /tests/func/test_api_exceptions.py

​ @pytest.mark.smoke
​ ​def​ ​test_list_raises​():
​     ​"""list() should raise an exception with wrong type param."""​
​     ​with​ pytest.raises(TypeError):
​         tasks.list_tasks(owner=123)
​ 
​ 
​ @pytest.mark.get
​ @pytest.mark.smoke
​ ​def​ ​test_get_raises​():
​     ​"""get() should raise an exception with wrong type param."""​
​     ​with​ pytest.raises(TypeError):
​         tasks.get(task_id=​'123'​)
```

#### Running the custom tagged tests

- To run those custom tagged tests, we can use `-m <CUSTOM_TAG>`
  - Using -m ’smoke’ runs both tests marked with @pytest.mark.smoke.
  - Using -m ’get’ runs the one test marked with @pytest.mark.get.

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​-m​​ ​​smoke​​ ​​test_api_exceptions.py​
​ =================== test session starts ===================
​ collected 7 items / 5 deselected
​ 
​ test_api_exceptions.py::test_list_raises PASSED     [ 50%]
​ test_api_exceptions.py::test_get_raises PASSED      [100%]
​ 
​ ========= 2 passed, 5 deselected in 0.03 seconds ==========
​ ​$ ​​pytest​​ ​​-v​​ ​​-m​​ ​​get​​ ​​test_api_exceptions.py​
​ =================== test session starts ===================
​ collected 7 items / 6 deselected
​ 
​ test_api_exceptions.py::test_get_raises PASSED      [100%]
​ 
​ ========= 1 passed, 6 deselected in 0.02 seconds ==========
```

#### Combining multiple markers

- The expression after -m can use `and`, `or`, and `not`` to combine multiple markers:

``` sh
# Running the test that had both smoke and get markers. 

​ ​$ ​​pytest​​ ​​-v​​ ​​-m​​ ​​"smoke and get"​​ ​​test_api_exceptions.py​
​ =================== test session starts ===================
​ collected 7 items / 6 deselected
​
​ test_api_exceptions.py::test_get_raises PASSED      [100%]
​
​ ========= 1 passed, 6 deselected in 0.02 seconds ==========
```

``` sh
# Running tests that had @pytest.mark.smoke but not @pytest.mark.get.

​ ​$ ​​pytest​​ ​​-v​​ ​​-m​​ ​​"smoke and not get"​​ ​​test_api_exceptions.py​
​ =================== test session starts ===================
​ collected 7 items / 6 deselected
​
​ test_api_exceptions.py::test_list_raises PASSED     [100%]
​
​ ========= 1 passed, 6 deselected in 0.02 seconds ==========
```

### Parametrized Testing

- Sending some values through a function and checking the output to make sure it’s correct is a common pattern in software testing.
- However, calling a function once with one set of values and one check for correctness isn’t enough to fully test most functions. - Parametrized testing is a way to send multiple sets of data through the same test and have pytest report if any of the sets failed.

To help understand the problem parametrized testing is trying to solve, let’s take a simple test for add():

``` py
ch2/tasks_proj/tests/func/test_add_variety.py

​ ​import​ ​pytest​
​ ​import​ ​tasks​
​ ​from​ ​tasks​ ​import​ Task
​ 
​ 
​ ​def​ ​test_add_1​():
​     ​"""tasks.get() using id returned from add() works."""​
​     task = Task(​'breathe'​, ​'BRIAN'​, True)
​     task_id = tasks.add(task)
​     t_from_db = tasks.get(task_id)
​     ​# everything but the id should be the same​
​     ​assert​ equivalent(t_from_db, task)
​ 
​ 
​ ​def​ ​equivalent​(t1, t2):
​     ​"""Check two tasks for equivalence."""​
​     ​# Compare everything but the id field​
​     ​return​ ((t1.summary == t2.summary) ​and​
​             (t1.owner == t2.owner) ​and​
​             (t1.done == t2.done))
​ 
​ 
​ @pytest.fixture(autouse=True)
​ ​def​ ​initialized_tasks_db​(tmpdir):
​     ​"""Connect to db before testing, disconnect after."""​
​     tasks.start_tasks_db(str(tmpdir), ​'tiny'​)
​     ​yield​
​     tasks.stop_tasks_db()
```

- When a Task object is created, its id field is set to None.
- After it’s added and retrieved from the database, the id field will be set.
- Therefore, we can’t just use == to check to see if our task was added and retrieved correctly.
- The equivalent() helper function checks all but the id field.
- The autouse fixture is included to make sure the database is accessible.
- Let’s make sure the test passes:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety.py::test_add_1​
​ =================== test session starts ===================
​ collected 1 item
​ 
​ test_add_variety.py::test_add_1 PASSED              [100%]
​ 
​ ================ 1 passed in 0.05 seconds =================
```

- The test seems reasonable.
- However, it’s just testing one example task.
- What if we want to test lots of variations of a task?
- No problem. We can use @pytest.mark.parametrize(argnames, argvalues) to pass lots of data through the same test, like this:

``` py
ch2/tasks_proj/tests/func/test_add_variety.py

​ @pytest.mark.parametrize(​'task'​,
​                          [Task(​'sleep'​, done=True),
​                           Task(​'wake'​, ​'brian'​),
​                           Task(​'breathe'​, ​'BRIAN'​, True),
​                           Task(​'exercise'​, ​'BrIaN'​, False)])
​ ​def​ ​test_add_2​(task):
​     ​"""Demonstrate parametrize with one parameter."""​
​     task_id = tasks.add(task)
​     t_from_db = tasks.get(task_id)
​     ​assert​ equivalent(t_from_db, task)
```

- The first argument to parametrize() is a string with a comma-separated list of names — ’task’, in our case.
- The second argument is a list of values, which in our case is a list of Task objects. pytest will run this test once for each task and report each as a separate test:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety.py::test_add_2​
​ =================== test session starts ===================
​ collected 4 items
​
​ test_add_variety.py::test_add_2[task0] PASSED       [ 25%]
​ test_add_variety.py::test_add_2[task1] PASSED       [ 50%]
​ test_add_variety.py::test_add_2[task2] PASSED       [ 75%]
​ test_add_variety.py::test_add_2[task3] PASSED       [100%]
​
​ ================ 4 passed in 0.05 seconds =================
```

This use of parametrize() works for our purposes. However, let’s pass in the tasks as tuples to see how multiple test parameters would work:

``` py
ch2/tasks_proj/tests/func/test_add_variety.py

​ @pytest.mark.parametrize(​'summary, owner, done'​,
​                          [(​'sleep'​, None, False),
​                           (​'wake'​, ​'brian'​, False),
​                           (​'breathe'​, ​'BRIAN'​, True),
​                           (​'eat eggs'​, ​'BrIaN'​, False),
​                           ])
​ ​def​ ​test_add_3​(summary, owner, done):
​     ​"""Demonstrate parametrize with multiple parameters."""​
​     task = Task(summary, owner, done)
​     task_id = tasks.add(task)
​     t_from_db = tasks.get(task_id)
​     ​assert​ equivalent(t_from_db, task)
```

When you use types that are easy for pytest to convert into strings, the test identifier uses the parameter values in the report to make it readable:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety.py::test_add_3​
​ =================== test session starts ===================
​ collected 4 items
​
​ test_add_variety.py::test_add_3[sleep-None-False] PASSED [ 25%]
​ test_add_variety.py::test_add_3[wake-brian-False] PASSED [ 50%]
​ test_add_variety.py::test_add_3[breathe-BRIAN-True] PASSED [ 75%]
​ test_add_variety.py::test_add_3[eat eggs-BrIaN-False] PASSED [100%]
​
​ ================ 4 passed in 0.05 seconds =================
```

You can use that whole test identifier — called a node in pytest terminology — to re-run the test if you want:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety.py::test_add_3[sleep-None-False]​
​ =================== test session starts ===================
​ collected 1 item
​
​ test_add_variety.py::test_add_3[sleep-None-False] PASSED [100%]
​
​ ================ 1 passed in 0.03 seconds =================
Be sure to use quotes if there are spaces in the identifier:

​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​"test_add_variety.py::test_add_3[eat eggs-BrIaN-False]"​
​ =================== test session starts ===================
​ collected 1 item
​
​ test_add_variety.py::test_add_3[eat eggs-BrIaN-False] PASSED [100%]
​
​ ================ 1 passed in 0.03 seconds =================
```

Now let’s go back to the list of tasks version, but move the task list to a variable outside the function:

``` py
ch2/tasks_proj/tests/func/test_add_variety.py

​ tasks_to_try = (Task(​'sleep'​, done=True),
​                 Task(​'wake'​, ​'brian'​),
​                 Task(​'wake'​, ​'brian'​),
​                 Task(​'breathe'​, ​'BRIAN'​, True),
​                 Task(​'exercise'​, ​'BrIaN'​, False))
​
​
​ @pytest.mark.parametrize(​'task'​, tasks_to_try)
​ ​def​ ​test_add_4​(task):
​     ​"""Slightly different take."""​
​     task_id = tasks.add(task)
​     t_from_db = tasks.get(task_id)
​     ​assert​ equivalent(t_from_db, task)
```

It’s convenient and the code looks nice. But the readability of the output is hard to interpret:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety.py::test_add_4​
​ =================== test session starts ===================
​ collected 5 items
​
​ test_add_variety.py::test_add_4[task0] PASSED       [ 20%]
​ test_add_variety.py::test_add_4[task1] PASSED       [ 40%]
​ test_add_variety.py::test_add_4[task2] PASSED       [ 60%]
​ test_add_variety.py::test_add_4[task3] PASSED       [ 80%]
​ test_add_variety.py::test_add_4[task4] PASSED       [100%]
​
​ ================ 5 passed in 0.06 seconds =================
```

The readability of the multiple parameter version is nice, but so is the list of Task objects. To compromise, we can use the ids optional parameter to parametrize() to make our own identifiers for each task data set. The ids parameter needs to be a list of strings the same length as the number of data sets. However, because we assigned our data set to a variable name, tasks_to_try, we can use it to generate ids:

``` py
ch2/tasks_proj/tests/func/test_add_variety.py

​ task_ids = [​'Task({},{},{})'​.format(t.summary, t.owner, t.done)
​             ​for​ t ​in​ tasks_to_try]
​
​
​ @pytest.mark.parametrize(​'task'​, tasks_to_try, ids=task_ids)
​ ​def​ ​test_add_5​(task):
​     ​"""Demonstrate ids."""​
​     task_id = tasks.add(task)
​     t_from_db = tasks.get(task_id)
​     ​assert​ equivalent(t_from_db, task)
```

Let’s run that and see how it looks:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety.py::test_add_5​
​ =================== test session starts ===================
​ collected 5 items
​
​ test_add_variety.py::test_add_5[Task(sleep,None,True)] PASSED [ 20%]
​ test_add_variety.py::test_add_5[Task(wake,brian,False)0] PASSED [ 40%]
​ test_add_variety.py::test_add_5[Task(wake,brian,False)1] PASSED [ 60%]
​ test_add_variety.py::test_add_5[Task(breathe,BRIAN,True)] PASSED [ 80%]
​ test_add_variety.py::test_add_5[Task(exercise,BrIaN,False)] PASSED [100%]
​
​ ================ 5 passed in 0.06 seconds =================
```

Note that the second and third tasks are actually duplicates of eachother and generate the same task id. To be able to tell them apart, pytest added a unique index to each, 0 and 1. The custom test identifiers can be used to run tests:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​"test_add_variety.py::test_add_5[Task(exercise,BrIaN,False)]"​
​ =================== test session starts ===================
​ collected 1 item
​
​ test_add_variety.py::test_add_5[Task(exercise,BrIaN,False)] PASSED [100%]
​
​ ================ 1 passed in 0.05 seconds =================
```

We definitely need quotes for these identifiers; otherwise, the brackets and parentheses will confuse the shell.

You can apply parametrize() to classes as well. When you do that, the same data sets will be sent to all test methods in the class:

``` py
ch2/tasks_proj/tests/func/test_add_variety.py

​ @pytest.mark.parametrize(​'task'​, tasks_to_try, ids=task_ids)
​ ​class​ TestAdd():
​     ​"""Demonstrate parametrize and test classes."""​
​
​     ​def​ ​test_equivalent​(self, task):
​         ​"""Similar test, just within a class."""​
​         task_id = tasks.add(task)
​         t_from_db = tasks.get(task_id)
​         ​assert​ equivalent(t_from_db, task)
​ ​def​ ​test_valid_id​(self, task):
​     ​"""We can use the same data for multiple tests."""​
​     task_id = tasks.add(task)
​     t_from_db = tasks.get(task_id)
​     ​assert​ t_from_db.id == task_id
```

Here it is in action:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety.py::TestAdd​
​ =================== test session starts ===================
​ collected 10 items
​
​ test_add_variety.py::TestAdd::test_equivalent[Task(sleep,None,True)] PASSED
​ test_add_variety.py::TestAdd::test_equivalent[Task(wake,brian,False)0] PASSED
​ test_add_variety.py::TestAdd::test_equivalent[Task(wake,brian,False)1] PASSED
​ test_add_variety.py::TestAdd::test_equivalent[Task(breathe,BRIAN,True)] PASSED
​ test_add_variety.py::TestAdd::test_equivalent[Task(exercise,BrIaN,False)] PASSED
​ test_add_variety.py::TestAdd::test_valid_id[Task(sleep,None,True)] PASSED
​ test_add_variety.py::TestAdd::test_valid_id[Task(wake,brian,False)0] PASSED
​ test_add_variety.py::TestAdd::test_valid_id[Task(wake,brian,False)1] PASSED
​ test_add_variety.py::TestAdd::test_valid_id[Task(breathe,BRIAN,True)] PASSED
​ test_add_variety.py::TestAdd::test_valid_id[Task(exercise,BrIaN,False)] PASSED
​
​ ================ 10 passed in 0.10 seconds ================
```

You can also identify parameters by including an id right alongside the parameter value when passing in a list within the @pytest.mark.parametrize() decorator. You do this with pytest.param(<value>, id=”something”) syntax:

``` py
ch2/tasks_proj/tests/func/test_add_variety.py

​ @pytest.mark.parametrize(​'task'​, [
​     pytest.param(Task(​'create'​), id=​'just summary'​),
​     pytest.param(Task(​'inspire'​, ​'Michelle'​), id=​'summary/owner'​),
​     pytest.param(Task(​'encourage'​, ​'Michelle'​, True), id=​'summary/owner/done'​)])
​ ​def​ ​test_add_6​(task):
​     ​"""Demonstrate pytest.param and id."""​
​     task_id = tasks.add(task)
​     t_from_db = tasks.get(task_id)
​     ​assert​ equivalent(t_from_db, task)
```

In action:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/func​
​ ​$ ​​pytest​​ ​​-v​​ ​​test_add_variety.py::test_add_6​
​ =================== test session starts ===================
​ collected 3 items
​
​ test_add_variety.py::test_add_6[just summary] PASSED [ 33%]
​ test_add_variety.py::test_add_6[summary/owner] PASSED [ 66%]
​ test_add_variety.py::test_add_6[summary/owner/done] PASSED [100%]
​
​ ================ 3 passed in 0.06 seconds =================
```

This is useful when the id cannot be derived from the parameter value.
