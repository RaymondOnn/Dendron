---
id: azmp35c7gkpov8qldsofgh0
title: usage
desc: ''
updated: 1697199716516
created: 1692087412356
---

## Best Practices

[Youtube link](https://www.youtube.com/watch?v=lX9UQp2NwTk&ab_channel=ArjanCodes)

### Ensure that classes are not too large

- Split the large class into multiple smaller classes
- Think of classes as being either **data-focused** OR **behaviour-focused**
  - Data-focused classes mainly intended as structured information
  - Behaviour-focused classes are really about grouping methods
- Refactor functions not part of the class into its own class
    - e.g. instead of the `Person` class having a `send_email_service` function where email message is created and sent, rewrite it into an `EmailService` class and have the service do the email things

<br>

<table>
  <tr>
    <th>Before</th>
    <th>After</th>
  </tr>
  <tr>
    <td>

``` py
# Before:

@dataclass
class Person:
    name:str
    age:str
    address_line_1:str
    address_line_2:str
    city: str
    country: str
    postal_code: str
    email: str
    phone_number: str

person = Person(...)    
```

</td>
<td>

``` py

@dataclass
class Address:
    address_line_1:str
    address_line_2:str
    city: str
    country: str
    postal_code: str

    def ...
    
@dataclass
class Person:
    name:str
    age:str
    address: Address
    email: str
    phone_number: str

address = Address(...)
person = Person(..., address=address, ...)    
```

</td>
  </tr>
</table>

### Make your classes easy to use

- One way to do it is relying on property and dunder methods
  - @cached_property: compute once and cache value
- Alternatively, we can shift the methods out before the classes and rewrite as pure functions
  - @lru_cache

<table>
  <tr>
    <th>Before</th>
    <th>After</th>
  </tr>
  <tr>
    <td>

``` py

@dataclass
class Stats:
    age: int
    gender: str
    height: float
    weight: float

    def get_bmi(self) -> float:
        return self.weight / (self.height**2)

    def get_bmi_category(self) -> str:
        if self.get_bmi() < 18.5:
            return 'Underweight'
        elif self.get_bmi() < 25:
            return 'Normal'
        elif self.get_bmi() < 30:
            return 'Overweight'
        else:
            return 'Obese'              
```

</td>
<td>

``` py
# Method 1:

@dataclass
class Stats:
    ...
    @property
    def bmi(self) -> float:
        return self.weight / (self.height**2)
    
    @property
    def bmi_category(self) -> str:
        if self.bmi < 18.5:
            return 'Underweight'
        elif self.bmi < 25:
            return 'Normal'
        elif self.bmi < 30:
            return 'Overweight'
        else:
            return 'Obese'    

# Method 2:
@lru_cache   # cache results specific to a set of values
def get_bmi(weight: float, height: float) -> float:
    return weight / (height**2)

@lru_cache
def get_bmi_category(bmi_value: float) -> str:
    if self.bmi_value < 18.5:
        return 'Underweight'
    elif self.bmi_value < 25:
        return 'Normal'
    elif self.bmi_value < 30:
        return 'Overweight'
    else:
        return 'Obese' 

class Stats:
    ...


def main():
    bmi_value = get_bmi(Stats.weight, stats.height)


```

</td>
  </tr>
</table>

### Use dependency Injection

- Accepting objects as inputs
- Introduce abstraction to decouple completely

<table>
  <tr>
    <th>Before</th>
    <th>After</th>
  </tr>
  <tr>
    <td>

``` py

@dataclass
class Person:
    ...
    def update_email(self, email: str) -> None:
        self.email = email
        email_service = EmailService(...)
        email_service.send_message(...)            
```

</td>
<td>

``` py
# Method 1:
# this change allows the use of different email services
@dataclass
class Person:
    ...
    def update_email(self, email: str, email_service: EmailService) -> None:
        self.email = email
        email_service.send_message(...) 

# ----------------------------------------------------------------------------

# Method 2:
class EmailSender(Protocol):
    def send_message(...) -> None:
        ...
@dataclass
class Person:
    ...
    def update_email(self, email: str, email_service: EmailSender) -> None:
        self.email = email
        email_service.send_message(...)                 
```

</td>
  </tr>
</table>

### Do you really need a class?

- if you only need one instance of the class, better to use a module with functions

<table>
  <tr>
    <th>Before</th>
    <th>After</th>
  </tr>
  <tr>
    <td>

``` py
# service.py
class EmailService:
    def __init__(...):
        ...      

    def send_message(...):
        ...       
```

</td>
<td>

``` py
# service.py
def create_email_message(...) -> EmailMessage:
    ...
    return msg

def send_email(...) -> None:
    msg = create_email_message(...)
    with ... as server:
        server.send_message()    

# main.py        
from service_v2 import send_email

class EmailSender(Protocol):
    def __call__(..)
        ...

@dataclass
class Person:
    ...
    def update_email(self, email: str, send_message_fx: EmailSender) -> None:
        self.email = email
        send_message_fx(...)  

def main():
    send_message = partial(send_email, ...)
    person.update_email( '123@abc.com', send_message)
```

</td>
  </tr>
</table>

### Encapsulation: Hide data from user

- One way is to use double underscore methods e.g. `self.__data`
