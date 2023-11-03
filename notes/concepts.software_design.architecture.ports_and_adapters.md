---
id: bzpu5dzryl18ahawq2qroa4
title: ports_and_adapters
desc: ''
updated: 1696695522124
created: 1696692753330
---
<https://code.likeagirl.io/ports-and-adapters-in-python-domain-driven-design-patterns-2c8c5a3171c8>

### What are ports and adapters in Python?

- The core idea of this pattern is to separate the application’s core logic from the infrastructure details and external dependencies.
  - The application core consists of the business logic, which can be tested independently of the infrastructure
  - the adapters provide the interfaces to connect the core with the external world.
- A “port” represents an abstract interface that the application provides to interact with external components.
- An “adapter” implements this interface and handles the communication with the external component.
- Overall, the ports and adapters pattern is a useful approach for building flexible and modular applications, especially when dealing with complex or changing external dependencies.

#### An example: User authentication service for a web app

- Port:
  - An interface The application core would provide for the authentication service
  - The actual implementation of the service could be done using any number of technologies (such as a database, a third-party API, or a custom implementation).
- Adapter:
  - The adapter would handle the communication with the specific implementation of the service.

### Implementation

- Goal: Prints the output of the random number generator to the console
- We want to separate the core logic of generating the random number from the infrastructure details of printing to the console.

``` py
# core.py - contains the core logic of generating a random number
import random
class RandomNumberGenerator:
    def generate(self):
        return random.randint(1, 10)

# ports.py - defines the interface for the print adapter
class PrintAdapterPort:
    def print_number(self, number: int):
        raise NotImplementedError

# adapters.py - implements the print adapter
from ports import PrintAdapterPort
class ConsolePrintAdapter(PrintAdapterPort):
    def print_number(self, number: int):
        print(f"The generated number is: {number}")


# main.py - connects the core and adapter to create the application
from core import RandomNumberGenerator
from adapters import ConsolePrintAdapter

generator = RandomNumberGenerator()
adapter = ConsolePrintAdapter()

number = generator.generate()
adapter.print_number(number)

```

### Example
Let’s consider a task management application with the following components:
- Core domain logic: Handles the business rules and operations related to tasks.
- Input ports: Responsible for receiving input from external sources, such as a command-line interface or a web API.
- Output ports: Handle the persistence and retrieval of data, such as a database or an external task management API.
- To keep it simple, we’ll focus on the input and output ports in this example.

``` py
# input_ports.py --------------------------------------
class TaskInputPort:
    def create_task(self, task):
        raise NotImplementedError

    def get_task(self, task_id):
        raise NotImplementedError

# input port adapter
class ConsoleTaskInputAdapter(TaskInputPort):
    def create_task(self):
        return input("Enter a task: ")

    def get_task(self, task_id):
        return task_id        
        
# output_ports.py --------------------------------------
class TaskOutputPort:
    def display_task(self, task):
        raise NotImplementedError

    def save_task(self, task):
        raise NotImplementedError        

# output port adapter
class FileTaskOutputAdapter(TaskOutputPort):
    def display_task(self, task):
        print(f"Task: {task}")

    def save_task(self, task):
        with open("tasks.txt", "a") as file:
            file.write(f"{task}\n")        

# core,py --------------------------------------
class TaskApplication:
    def __init__(self, input_adapter, output_adapter):
        self.input_adapter = input_adapter
        self.output_adapter = output_adapter

    def create_task(self):
        task_description = self.input_adapter.create_task()
        task = f"[ ] {task_description}"
        self.output_adapter.display_task(task)
        self.output_adapter.save_task(task)

    def get_task(self, task_id):
        task = self.input_adapter.get_task(task_id)
        self.output_adapter.display_task(task) 


# main.py  --------------------------------------       
from input_ports import ConsoleTaskInputAdapter
from output_ports import FileTaskOutputAdapter
from application import TaskApplication

if __name__ == "__main__":
    input_adapter = ConsoleTaskInputAdapter()
    output_adapter = FileTaskOutputAdapter()
    app = TaskApplication(input_adapter, output_adapter)

    app.create_task()
    app.get_task(1)               
```