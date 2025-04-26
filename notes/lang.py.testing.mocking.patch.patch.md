---
id: h8jlgccsstac9gw3yjzz2k4
title: patch
desc: ''
updated: 1745688292059
created: 1745657123669
---
<https://www.youtube.com/watch?v=ww1UsGZV8fQ>

### What Is `patch()`?

- `patch()` is the main mocking mechanism in Python’s unittest.mock library.
- Used to replace objects temporarily during tests, typically with a MagicMock.
- Temporarily replaces a target attribute with a MagicMock.
- This allows you to:
  - Set return values
  - Set side effects
  - Assert calls
  - Avoid executing real code (e.g., writing to a database)

### When to Use patch()?

- Avoid making real calls during tests (e.g., writing to databases, deleting files).
- Keep tests precise by isolating units (e.g., patch function_b while testing function_a).

### Common Confusions

1. Identifying the target
   - Understand where the object is looked up.
   - Know what your module knows about the object.

2. How to use patch
   - Several syntaxes (decorators, context managers, manual)
   - Can be confusing when reading Stack Overflow answers

### Understanding Targets in `patch()`

- The Target Is a Path String
- The Target is the dotted path string pointing to the attribute you want to mock.

    ``` py
    @patch('my_module.db_write')
    def test_foo(mock_db_write):
        my_module.foo()
    ```

- Most common mistake: Targeting the wrong path.
- You must patch where the object is used, not where it’s defined.
- The string path should reflect where the object is looked up, not where it originates.

> Key rule: Patch where the object is used, not where it’s defined.

#### Example

1. Direct Import in Module:

    ``` py
    # module.py
    from db import db_write
    ```

   - Correct: `@patch('mymodule.db_write')`
   - Wrong: `@patch('db.db_write')`

2. Module Import:

    ``` py
    # my_module.py
    import db
    ```

    - Correct: `@patch('mymodule.db.db_write')`
  
- Imagine `patch()` replaces the reference in memory.
- When `my_module.foo()` calls `db_write()`, that reference is now pointing to a `MagicMock`.

#### Ways to use `patch()`?

1. Context Manager

    ``` py
    with patch('my_module.db_write') as mock_db_write:
        my_module.foo()
    ```

    - Scope: Only inside the `with` block
    - Good for short-lived or built-in patches
<br>
2. Function Decorator

    ``` py
    @patch(',y_module.db_write')
    def test_foo(mock_db_write)
        ...
    ```

    - Scope: Entire test function
<br>
3. Class Decorator

    ``` py
    @patch('my_module.db_write')
    class TestFoo(unittest.TestCase):
        def test_foo(self, mock_db_write):
            ...
    ```

    - Scope: Every test method in the class
<br>
4. Manual Start/Stop

    ``` py
    def setUp(self):
        self.patcher = patch('my_module.db_write')
        self.mock_db_write = self.patcher.start()
        self.addCleanup(self.patcher.stop)
    ```

    - Scope: Custom; you control lifecycle.
    - Use `addCleanup()` to ensure teardown even if exceptions occur.

<br>

| Syntax | Scope | Use case |
| --- | --- | --- |
| Context Manager | Code block | For short-lived mocks (e.g., built-ins) |
| Function Decorator | Single function | Most common and cleanest |
| Class Decorator | All methods in class | DRY principle, same mock for all tests |
| Manual Start/Stop | Flexible (custom scope) | Useful for setup/teardown reuse |

##### Common Pitfalls

- Forgetting to stop manual mocks
- Forgetting to pass the mock object to the function
- Incorrect target path (leads to mocking the wrong thing)

### Improving Mocks with `spec`, `autospec`, `spec_set`

- Problems with `MagicMock`:
  - Accepts any attribute or call, even typos
  - Won’t raise errors on misspelled methods or invalid call signatures

- Use `spec` or `autospec` as default practice to avoid silent test failures.
  - Restrict mocks to real attributes/methods.
  - Prevent silent failures from typos or wrong method signatures.
  - Always using `spec` is a good practice.
  - `autospec` is stricter but can cause issues if you have descriptors or properties that execute code.

#### `spec=True`

- Restricts mock to only attributes of the target object.
- Enforces method signatures.

#### `autospec=True`

- Deeper enforcement: understands nested attributes.
- Drawback: won’t see dynamically created attributes (e.g. in __init__).

#### `spec_set=True`

- Prevents setting nonexistent attributes.

### Advanced patch() Options

- `new_callable`: Replace target with any callable (e.g., property, lambda)

    ``` py
    @patch('module.attr', new_callable=PropertyMock)
    ```

- `new`: Replace with an actual object (not just mocks)—rarely used

    ``` py
    @patch('module.attr', new='Replacement String')
- `create=True`: Creates attribute if it doesn’t already exist (used with built-ins, rarely needed now)

    ``` py
    @patch('module.nonexistent_attr', create=True)
    ```

- `**kwargs`: Pass mock config directly (return_value, attributes, etc.)
    ``` py
    @patch('module.attr', return_value=42)
    @patch('module_attr', name+'MockName')
    ```

### Advanced Patch Usage (Mentioned but Not Covered)
- `patch.object`
- `patch.dict`
- `patch.multiple`