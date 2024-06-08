---
id: u5hnivniu1226hnz69ihowo
title: Side_effect
desc: ''
updated: 1713930224556
created: 1713930189456
---

### Side effect functions and iterables
- `side_effect` can also be set to a function or an iterable. 
- The use case for `side_effect` as an iterable is where your mock is going to be called several times, and you want each call to return a different value. 
- When you set `side_effect` to an iterable every call to the mock returns the next value from the iterable:
    ``` py
    mock = MagicMock(side_effect=[4, 5, 6])
    mock()
    4
    mock()
    5
    mock()
    6
    ```
- For more advanced use cases, like dynamically varying the return values depending on what the mock is called with, `side_effect` can be a function. 
  - The function will be called with the same arguments as the mock. Whatever the function returns is what the call returns:

    ``` py
    vals = {(1, 2): 1, (2, 3): 2}
    def side_effect(\*args):
        return vals[args]

    mock = MagicMock(side_effect=side_effect)
    mock(1, 2)
    1
    mock(2, 3)
    2
    ```
    
### Using `side_effect` to return per file content
- `mock_open()` is used to patch `open()` method. 
- `side_effect` can be used to return a new Mock object per call. 
- This can be used to return different contents per file stored in a dictionary:
    ``` py
    DEFAULT = "default"
    data_dict = {"file1": "data1",
                "file2": "data2"}

    def open_side_effect(name):
        return mock_open(read_data=data_dict.get(name, DEFAULT))()

    with patch("builtins.open", side_effect=open_side_effect):
        with open("file1") as file1:
            assert file1.read() == "data1"

        with open("file2") as file2:
            assert file2.read() == "data2"

        with open("file3") as file2:
            assert file2.read() == "default"
    ```