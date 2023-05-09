---
id: 32lhwvbf7k0i8t0czi57oa6
title: Command
desc: ''
updated: 1680345421420
created: 1680344819893
---

### Command
- Command pattern decouples the object invoking a job from the one who knows
how to do it. 
- As mentioned in the GoF book, a good example is in menu items.
    - You have a menu that has lots of items. 
    - Each item is responsible for doing a
special thing and you want your menu item just call the execute method when
it is pressed. 
    - To achieve this you implement a command object with the execute
method for each menu item and pass to it.

<BR>

#### About the example
- We have a menu containing two items. 
- Each item accepts a file name, one hides the file and the other deletes it. Both items have an undo option.
- Each item is a MenuItem class that accepts the corresponding command as input and executes its execute method when it is pressed.
  
``` py
from typing import List, Union


class HideFileCommand:
    """
    A command to hide a file given its name
    """

    def __init__(self) -> None:
        # an array of files hidden, to undo them as needed
        self._hidden_files: List[str] = []

    def execute(self, filename: str) -> None:
        print(f"hiding {filename}")
        self._hidden_files.append(filename)

    def undo(self) -> None:
        filename = self._hidden_files.pop()
        print(f"un-hiding {filename}")


class DeleteFileCommand:
    """
    A command to delete a file given its name
    """

    def __init__(self) -> None:
        # an array of deleted files, to undo them as needed
        self._deleted_files: List[str] = []

    def execute(self, filename: str) -> None:
        print(f"deleting {filename}")
        self._deleted_files.append(filename)

    def undo(self) -> None:
        filename = self._deleted_files.pop()
        print(f"restoring {filename}")


class MenuItem:
    """
    The invoker class. Here it is items in a menu.
    """

    def __init__(self, command: Union[HideFileCommand, DeleteFileCommand]) -> None:
        self._command = command

    def on_do_press(self, filename: str) -> None:
        self._command.execute(filename)

    def on_undo_press(self) -> None:
        self._command.undo()


if __name__ == "__main__":
    item1 = MenuItem(DeleteFileCommand())
    item2 = MenuItem(HideFileCommand())
    
    # create a file named `test-file` to work with
    test_file_name = 'test-file'
    
    # deleting `test-file`
    item1.on_do_press(test_file_name) 
    -> Output:deleting test-file
    
    # restoring `test-file`
    item1.on_undo_press() 
    -> # Output: restoring test-file
    
    # hiding `test-file`
    item2.on_do_press(test_file_name) 
    -> # Output: hiding test-file
    
    # un-hiding `test-file`
    item2.on_undo_press() 
    -> # Output: un-hiding test-file

