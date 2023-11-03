---
id: tkgswruxqa0m1kzc4xtx7ub
title: assert
desc: ''
updated: 1698739796581
created: 1698738724805
---

### Using assert Statements

- When you write test functions, the normal Python assert statement is your primary tool to communicate test failure.
- With pytest, you can use `assert <expression>` with any expression. If the expression would evaluate to False if converted to a bool, the test would fail.
- pytest includes a feature called assert rewriting that intercepts assert calls and replaces them with something that can tell you more about why your assertions failed. Let’s see how helpful this rewriting is by looking at a few assertion failures:

``` py
ch2/tasks_proj/tests/unit/test_task_fail.py

​ ​"""Use the Task type to show test failures."""​
​ ​from​ ​tasks​ ​import​ Task
​ 
​ 
​ ​def​ ​test_task_equality​():
​     ​"""Different tasks should not be equal."""​
​     t1 = Task(​'sit there'​, ​'brian'​)
​     t2 = Task(​'do something'​, ​'okken'​)
​     ​assert​ t1 == t2
​ 
​ 
​ ​def​ ​test_dict_equality​():
​     ​"""Different tasks compared as dicts should not be equal."""​
​     t1_dict = Task(​'make sandwich'​, ​'okken'​)._asdict()
​     t2_dict = Task(​'make sandwich'​, ​'okkem'​)._asdict()
​     ​assert​ t1_dict == t2_dict
```

All of these tests fail, but what’s interesting is the traceback information:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj/tests/unit​
​ venv) $ pytest test_task_fail.py
​ =================== test session starts ===================
​ collected 2 items
​ 
​ test_task_fail.py FF                                [100%]
​ 
​ ======================== FAILURES =========================
​ ___________________ test_task_equality ____________________
​ 
​     def test_task_equality():
​         """Different tasks should not be equal."""
​         t1 = Task('sit there', 'brian')
​         t2 = Task('do something', 'okken')
​ ​>​​       ​​assert​​ ​​t1​​ ​​==​​ ​​t2​
​ E       AssertionError: assert Task(summary=...alse, id=None) ==
​ Task(summary='...alse, id=None)
​ E         At index 0 diff: 'sit there' != 'do something'
​ E         Use -v to get the full diff
​ 
​ test_task_fail.py:9: AssertionError
​ ___________________ test_dict_equality ____________________
​ 
​     def test_dict_equality():
​         """Different tasks compared as dicts should not be equal."""
​         t1_dict = Task('make sandwich', 'okken')._asdict()
​         t2_dict = Task('make sandwich', 'okkem')._asdict()
​ ​>​​       ​​assert​​ ​​t1_dict​​ ​​==​​ ​​t2_dict​
​ E       AssertionError: assert OrderedDict([...('id', None)]) ==
​ OrderedDict([(...('id', None)])
​ E         Omitting 3 identical items, use -vv to show
​ E         Differing items:
​ E         {'owner': 'okken'} != {'owner': 'okkem'}
​ E         Use -v to get the full diff
​ 
​ test_task_fail.py:16: AssertionError
​ ================ 2 failed in 0.07 seconds =================
```

- For each failing test,
  - the exact line of failure is shown with a > pointing to the failure.
  - The E lines show you extra information about the assert failure to help you figure out what went wrong.

I intentionally put two mismatches in test_task_equality(), but only the first was shown in the previous code. Let’s try it again with the -v flag, as suggested in the error message:

``` sh
​ ​$ ​​pytest​​ ​​-v​​ ​​test_task_fail.py::test_task_equality​
​ =================== test session starts ===================
​ collected 1 item
​
​ test_task_fail.py::test_task_equality FAILED        [100%]
​
​ ======================== FAILURES =========================
​ ___________________ test_task_equality ____________________
​
​     def test_task_equality():
​         """Different tasks should not be equal."""
​         t1 = Task('sit there', 'brian')
​         t2 = Task('do something', 'okken')
​ ​>​​       ​​assert​​ ​​t1​​ ​​==​​ ​​t2​
​ E       AssertionError: assert Task(summary=...alse, id=None) ==
​ Task(summary='...alse, id=None)
​ E         At index 0 diff: 'sit there' != 'do something'
​ E         Full diff:
​ E         - Task(summary='sit there', owner='brian', done=False, id=None)
​ E         ?                ^^^  ^^^          ^^^^
​ E         + Task(summary='do something', owner='okken', done=False, id=None)
​ E         ?               +++ ^^^  ^^^          ^^^^
​
​ test_task_fail.py:9: AssertionError
​ ================ 1 failed in 0.07 seconds =================
```

pytest not only found both differences, but it also showed us exactly where the differences are.

### Expecting Exceptions

- Exceptions may be raised when things go wrong
- To make sure these functions raise exceptions if called incorrectly, we can intentionally cause exceptions, and use with `pytest.raises(<expected exception>)`, like this:

``` py
# /tests/func/test_api_exceptions.py

​ ​import​ ​pytest​
​ ​import​ ​tasks​
​ 
​ 
​ ​def​ ​test_add_raises​():
​     ​"""add() should raise an exception with wrong type param."""​
​     ​with​ pytest.raises(TypeError):   # expecting TypeError Exception to be raised in the following code block
​         tasks.add(task=​'not a Task object'​)
```

- with `pytest.raises(TypeError)`: statement says that whatever is in the next block of code should raise a TypeError exception.
  - If no exception is raised, the test fails.
  - If the test raises a different exception, it fails.

- You can also check the parameters to the exception.
  - For start_tasks_db(db_path, db_type), not only does db_type need to be a string, it really has to be either ’tiny’ or ’mongo’.
  - You can check to make sure the exception message is correct by adding as excinfo:

``` py
# /tests/func/test_api_exceptions.py

​ ​def​ ​test_start_tasks_db_raises​():
​     ​"""Make sure unsupported db raises an exception."""​
​     ​with​ pytest.raises(ValueError) ​as​ excinfo:
​         tasks.start_tasks_db(​'some/great/path'​, ​'mysql'​)
​     exception_msg = excinfo.value.args[0]
​     ​assert​ exception_msg == ​"db_type must be a 'tiny' or 'mongo'"​
```

- This allows us to look at the exception more closely.
- The variable name you put after as (excinfo in this case) is filled with information about the exception, and is of type ExceptionInfo.
