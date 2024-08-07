---
id: qoyvkx1m5ptsf4ab1g51vjg
title: Patch
desc: ''
updated: 1713930152948
created: 1713929746039
---

### Patch Decorators
> Note:   With `patch()` it matters that you patch objects in the namespace where they are looked up. This is normally straightforward, but for a quick guide read where to patch.

- A common need in tests is to patch a class attribute or a module attribute, for example patching a builtin or patching a class in a module to test that it is instantiated. 
  - Modules and classes are effectively global, so patching on them has to be undone after the test or the patch will persist into other tests and cause hard to diagnose problems.

- mock provides three convenient decorators for this: `patch()`, `patch.object()` and `patch.dict()`. 
    - `patch` takes a single string, of the form `package.module.Class.attribute` to specify the attribute you are patching. 
        - It also optionally takes a value that you want the attribute (or class or whatever) to be replaced with. 
    - `patch.object` takes an object and the name of the attribute you would like patched, plus optionally the value to patch it with.

    ``` py
    original = SomeClass.attribute
    @patch.object(SomeClass, 'attribute', sentinel.attribute)
    def test():
        assert SomeClass.attribute == sentinel.attribute

    test()
    assert SomeClass.attribute == original

    @patch('package.module.attribute', sentinel.attribute)
    def test():
        from package.module import attribute
        assert attribute is sentinel.attribute

    test()
    ```
- If you are patching a module (including builtins) then use `patch()` instead of `patch.object()`:
    ``` py
    mock = MagicMock(return_value=sentinel.file_handle)
    with patch('builtins.open', mock):

        handle = open('filename', 'r')

    mock.assert_called_with('filename', 'r')
    assert handle == sentinel.file_handle, "incorrect file handle returned"
    ```
- The module name can be ‘dotted’, in the form package.module if needed:
    ``` py
    @patch('package.module.ClassName.attribute', sentinel.attribute)
    def test():
        from package.module import ClassName
        assert ClassName.attribute == sentinel.attribute

    test()
    ```
    
- A nice pattern is to actually decorate test methods themselves:
    ``` py
    class MyTest(unittest.TestCase):

        @patch.object(SomeClass, 'attribute', sentinel.attribute)
        def test_something(self):
            self.assertEqual(SomeClass.attribute, sentinel.attribute)

    original = SomeClass.attribute
    MyTest('test_something').test_something()
    assert SomeClass.attribute == original
    ```
- If you want to patch with a Mock, you can use patch() with only one argument (or patch.object() with two arguments). The mock will be created for you and passed into the test function / method:
    ``` py
    class MyTest(unittest.TestCase):
        
        @patch.object(SomeClass, 'static_method')
        def test_something(self, mock_method):
            SomeClass.static_method()
            mock_method.assert_called_with()

    MyTest('test_something').test_something()
    ```
- You can stack up multiple `patch` decorators using this pattern:
    ``` py
    class MyTest(unittest.TestCase):
        
        @patch('package.module.ClassName1')
        @patch('package.module.ClassName2')
        def test_something(self, MockClass2, MockClass1):
            self.assertIs(package.module.ClassName1, MockClass1)
            self.assertIs(package.module.ClassName2, MockClass2)

    MyTest('test_something').test_something()
    ```
    - When you nest patch decorators the mocks are passed in to the decorated function in the same order they applied (the normal Python order that decorators are applied). 
    - This means from the bottom up, so in the example above the mock for `test_module.ClassName2` is passed in first.

- `patch.dict()`: For setting values in a dictionary just during a scope and restoring the dictionary to its original state when the test ends:
    ``` py
    foo = {'key': 'value'}
    original = foo.copy()
    with patch.dict(foo, {'newkey': 'newvalue'}, clear=True):
        assert foo == {'newkey': 'newvalue'}

    assert foo == original
    ```

- `patch`, `patch.object` and `patch.dict` can all be used as context managers.
    - Where you use `patch()` to create a mock for you, you can get a reference to the mock using the `as` form of the `with` statement:
    ``` py
    class ProductionClass:
        def method(self):
            pass

    with patch.object(ProductionClass, 'method') as mock_method:
        mock_method.return_value = None
        real = ProductionClass()
        real.method(1, 2, 3)

    mock_method.assert_called_with(1, 2, 3)
    ```
As an alternative patch, patch.object and patch.dict can be used as class decorators. When used in this way it is the same as applying the decorator individually to every method whose name starts with “test”.
