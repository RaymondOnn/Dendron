---
id: w3yayj5pexzb8q0ng0r3fda
title: Validator
desc: ''
updated: 1733067189583
created: 1697134566725
---
https://levelup.gitconnected.com/validator-pattern-do-you-know-how-to-validate-your-data-properly-50edc5b3c6c6

Validator Pattern is a classic design pattern that is extremely useful if you use it properly. Having a great validation system is a really big deal if you want to deliver a great product. Let’s see how we can build a great validation system. At the end of this guide we will put our knowledge into practice and create an Advanced Validation System that uses Custom Rules and Conditional Rules for our Validator.

Practical Demonstration
First of all, let’s say we have a marketing product that stores persons to a database. We need to make sure the data is valid because we want to start a marketing campaign and send emails to those persons in the future.

Project Structure
This is our project structure, nothing to explain here.

├── domain
│   ├── __init__.py
│   └── person.py           # Our Person class 
├── main.py                 # Main Application
└── validator
    ├── __init__.py
    ├── exceptions.py       # Custom Validation Exceptions
    ├── interface.py        # Validator Interface / Base Class
    └── person_validator.py # Validator for Person class
domain/person.py

This is our person class definition with all its fields.

class Person:
    def __init__(
        self,
        first_name: str,
        last_name: str,
        age: int,
        email: str
    ) -> None:
        self.first_name = first_name
        self.last_name = last_name
        self.age = age
        self.email = email

    def __str__(self) -> str:
        return (
            f"{self.first_name} "
            f"{self.last_name} - "
            f"{self.age}, "
            f"{self.email}\n"
        )
Booleans Vs. Exceptions
Before diving deep into data validation, let’s first look at this two approaches:

""" Data validation with boolean methods """

def validate_bool(value: str, min_length: int, max_length: int) -> bool:
    return min_length <= len(value) <= max_length


if not validate_bool("asd", 4, 7):
    print("Invalid Data")



""" Data validation with exceptions """
def validate_exception(value: str, min_length: int, max_length: int) -> None:
    if not min_length <= len(value) <= max_length:
        raise ValueError(
            f"Data length should be between {min_length} and {max_length}"
        )

try:
    validate_exception("asd", 4, 7)
except ValueError as e:
    print(str(e))
Both methods achieve the same thing, validates the data, but both have trade-offs. For example, the booleans are simple, explicit, and fast, but they lack a major thing: They cannot provide much information about why the validation failed. On the other hand Exceptions are a bit more complicated, they alter the program’s control flow, which is something that we want in this case, they are very rich in information, allow for better debugging, allow better control flow and they also makes the code more cleaner. The biggest downside is that they are not as fast as Booleans and if you don’t handle them well it can complicate your program flow. The last one is not a big deal, we are great programmers so in our case this shouldn’t happen.

For simple validations it’s ok to use Booleans, but if you want to create advanced validation systems, you should really take a look into exceptions. In this guide we will be using Exceptions since we want to go into advanced stuff. In your future systems, you can also use a mix of the two.

Naive Validation Techniques
Usually you would be tempted to move the validation logic into setters, or to create custom methods on the object itself to handle the validation. This approach would lead you to many problems in the future, like for example, you would need to do some sort of checking if your object would raise an error when you assign some values to it. Or you just need to validate if the object is valid only when you store it to database. Think about a simple editor where you can leave the email field empty while you are working, but it will be required to be set when you save the file.

class Person:
    def __init__(
        self,
        first_name: str,
        last_name: str,
        age: int,
        email: str
    ) -> None:
        self.first_name = first_name
        self.last_name = last_name
        self.age = age
        self.email = email

    def validate(self) -> None:
        # Type checking
        if not isinstance(self.first_name, str):
            raise TypeError("First name must be a string")
        if not isinstance(self.last_name, str):
            raise TypeError("Last name must be a string")
        if not isinstance(self.age, int):
            raise TypeError("Age must be an integer")
        if not isinstance(self.email, str):
            raise TypeError("Email must be a string")

        # Age validation
        if self.age < 15 or self.age > 100:
            raise ValueError("Age is not valid")

        # Email validation
        if "@" not in self.email or "." not in self.email:
            raise ValueError("Email is not valid")

    def __str__(self) -> str:
        return (
            f"{self.first_name} "
            f"{self.last_name} - "
            f"{self.age} years, "
            f"{self.email}\n"
        )
This is one method on how you can handle the validation of the person object. However, as you can see, this can become quite messy if you have to handle multiple validations, the validate method can become endless.

class Person:
    def __init__(
        self,
        first_name: str,
        last_name: str,
        age: int,
        email: str
    ) -> None:
        self.first_name = first_name
        self.last_name = last_name
        self.age = age
        self.email = email

    def validate(self) -> None:
        self._validate_types()
        self._validate_age()
        self._validate_email()

    def _validate_types(self) -> None:
        if not isinstance(self.first_name, str):
            raise TypeError("First name must be a string")
        if not isinstance(self.last_name, str):
            raise TypeError("Last name must be a string")
        if not isinstance(self.age, int):
            raise TypeError("Age must be an integer")
        if not isinstance(self.email, str):
            raise TypeError("Email must be a string")

    def _validate_age(self) -> None:
        if self.age < 15 or self.age > 100:
            raise ValueError("Age is not valid")

    def _validate_email(self) -> None:
        if "@" not in self.email or "." not in self.email:
            raise ValueError("Email is not valid")

    def __str__(self) -> str:
        return (
            f"{self.first_name} "
            f"{self.last_name} - "
            f"{self.age} years, "
            f"{self.email}\n"
        )
You would say that splitting into multiple functions will help, but the truth is, this will soon become spaghetti code. And this is just the start, the you would be tempted to add other logic to the object, let’s say saving it to a file, with a save() method and so on. So, this approach is also to be avoided.

class Person:
    def __init__(
        self,
        first_name: str,
        last_name: str,
        age: int,
        email: str
    ) -> None:
        self.first_name = first_name
        self.last_name = last_name
        self.age = age
        self.email = email

    @property
    def first_name(self) -> str:
        return self._first_name

    @first_name.setter
    def first_name(self, value) -> None:
        if not isinstance(value, str):
            raise TypeError("First name must be a string")

        self._first_name = value

    @property
    def last_name(self) -> str:
        return self._last_name

    @last_name.setter
    def last_name(self, value: str) -> None:
        if not isinstance(value, str):
            raise TypeError("Last name must be a string")

        self._last_name = value

    @property
    def age(self) -> int:
        return self._age

    @age.setter
    def age(self, value: int) -> None:
        if not isinstance(value, int):
            raise TypeError("Age must be an integer")
        if value < 15 or value > 100:
            raise ValueError("Age is not valid")

        self._age = value

    @property
    def email(self) -> str:
        return self._email

    @email.setter
    def email(self, value: str) -> None:
        if not isinstance(value, str):
            raise TypeError("Email must be a string")
        if "@" not in value or "." not in value:
            raise ValueError("Email is not valid")

        self._email = value

    def __str__(self) -> str:
        return (
            f"{self.first_name} "
            f"{self.last_name} - "
            f"{self.age} years, "
            f"{self.email}\n"
        )
This is another approach that can do the validation part just fine. However, this can also become a big overhead. If you do validations in the setter, you would also be tempted to do other sorts of business logic there too. Another downside is that the validation part will be called every time you set some fields to your model, that means you need to check for errors every time you make changes to your object fields so you will end up having tons of try-except blocks everywhere you perform some changes to the object.

From a performance point of view, this is not optimal, because maybe you want to validate the object only before saving it to a database, not all the time. Using validations in setters, you will perform validations all the time. Take for example a Form, you need to validate the data only once the “Send” button is pressed by the user. If the user enters some invalid data it will display a pop-up error message. Think about displaying constant pop-ups to the user when he is editing the form because the fields are not valid yet. A good user experience would be just to display and perform the validations once the user finishes the editing and wants to save the object to database.

Validator Pattern — Simple Example First
This is basically a class called Validator that has the purpose of validating our object. First we will try to create a simple class for our Validator and then we will create an entire Validation System where we can add and create validation rules for our objects.

validator/interface.py
This is the basic interface of our validator. Nothing to explain here.

from typing import Protocol
from domain.person import Person


class IValidator(Protocol):
    """ Validator for Person """

    def validate(self, data: Person) -> None:
        """ Validate the person """
        raise NotImplementedError("validate not implemented")
validator/person_validator.py
from domain.person import Person
from .interface import IValidator


class PersonValidator(IValidator):
    def __init__(self) -> None:
        super().__init__()

    def validate(self, data: Person) -> None:
        # Type checking
        if not isinstance(data.first_name, str):
            raise TypeError("First name must be a string")
        if not isinstance(data.last_name, str):
            raise TypeError("Last name must be a string")
        if not isinstance(data.age, int):
            raise TypeError("Age must be an integer")
        if not isinstance(data.email, str):
            raise TypeError("Email must be a string")

        # Age validation
        if data.age < 15 or data.age > 100:
            raise ValueError("Age is not valid")

        # Email validation
        if "@" not in data.email or "." not in data.email:
            raise ValueError("Email is not valid")
This is a simple validator, just to prove the concept, in the next examples we will turn this into an advanced validation system. As you can see, we made a separate with the purpose of validation our data. This is good, because now, everything that is related to the data validation will be in one place and we can make any changes in one place and it will be reflected in our entire application.

main.py
from domain.person import Person
from validator.person_validator import PersonValidator
import sys


def store_to_database(person: Person) -> None:
    """ Saves the person to database """
    print(f"{person.first_name} {person.last_name} stored to database!")


def main() -> None:
    validator = PersonValidator()

    person = Person(
        first_name="John",
        last_name="Doe",
        age=24,
        email="johndoe@gmail.com"
    )

    try:
        validator.validate(person)
    except (ValueError, TypeError) as e:
        sys.exit(f"Validation Error: {str(e)}")
    
    store_to_database(person)


if __name__ == "__main__":
    main()
This is our basic main to demonstrate how our basic validator pattern works.

Custom Exceptions For Better Error Handling
If we want to take full control of our validation system, we need custom exceptions. Custom exceptions will help us understand what kind of error we are catching. In the previous example we are catching ValueError and TypeError but this could lead to unwanted behaviours, that i will talk about below. It would be great to throw exceptions like InvalidPerson, InvalidNameError, InvalidEmailError, etc.

Be very careful when dealing with Exceptions.
Like how I already said, using for example ValueError as a default exception, will lead us to some unwanted bugs.

Take for example this simple validation method:

def validate_positive_number(my_string_number: str) -> None:
    converted_number = int(my_string_number)

    if converted_number < 0:
        raise ValueError("Number should be positive.")
Did you spot the bug? Well, our method will raise ValueError, but not because the number is negative. If we set my_string_number to “-102a” our conversion line converted_number = int(my_string_number) will raise ValueError because it can’t convert the string -102a to an int. That’s an unwanted behaviour because this error is not raised by us, it’s raised by a bug in our code.

To avoid this kind of bugs, we need to use Custom Exceptions so we can have full control over the exceptions that are raised.

validator/exceptions.py
These are our custom exceptions, keep in mind that InvalidPerson exception will be used later in the next example.

from typing import List, Optional

class InvalidPerson(Exception):
    """Invalid person exception"""
    
    def __init__(
            self, 
            message: str, 
            exceptions: Optional[List[Exception]] = None
    ) -> None:
        """Initialize the exception"""
        
        super().__init__()
        self.message = message
        self.exceptions = exceptions


class InvalidEmail(Exception):
    """Invalid email exception"""
    
    def __init__(self, message: str) -> None:
        """Initialize the exception"""
        super().__init__()
        self.message = message


class InvalidAge(Exception):
    """Invalid age exception"""
    
    def __init__(self, message: str) -> None:
        """Initialize the exception"""
        super().__init__()
        self.message = message


class InvalidFieldType(Exception):
    """Invalid email exception"""
    
    def __init__(self, message: str) -> None:
        """Initialize the exception"""
        super().__init__()
        self.message = message
validator/person_validator.py
from domain.person import Person
from .interface import IValidator
from .exceptions import InvalidAge
from .exceptions import InvalidEmail
from .exceptions import InvalidFieldType


class PersonValidator(IValidator):
    def __init__(self) -> None:
        super().__init__()

    def validate(self, data: Person) -> None:
        # Type checking
        if not isinstance(data.first_name, str):
            raise InvalidFieldType("First name must be a string")
        if not isinstance(data.last_name, str):
            raise InvalidFieldType("Last name must be a string")
        if not isinstance(data.age, int):
            raise InvalidFieldType("Age must be an integer")
        if not isinstance(data.email, str):
            raise InvalidFieldType("Email must be a string")

        # Age validation
        if data.age < 15 or data.age > 100:
            raise InvalidAge("Age needs to be between 10 and 100")

        # Email validation
        if "@" not in data.email or "." not in data.email:
            raise InvalidEmail("Email is not valid")
Now everything is much clearer. We know what kind of exception we are raising and what kind of exception we are expecting.

main.py
from domain.person import Person
from validator.person_validator import PersonValidator
from validator.exceptions import InvalidAge
from validator.exceptions import InvalidEmail
from validator.exceptions import InvalidFieldType
import sys


def store_to_database(person: Person) -> None:
    """ Saves the person to database """
    print(f"{person.first_name} {person.last_name} stored to database!")


def main():
    validator = PersonValidator()

    person = Person(
        first_name="John",
        last_name="Doe",
        age=24,
        email="johndoe@gmail.com"
    )

    try:
        validator.validate(person)
    except (InvalidAge, InvalidEmail, InvalidFieldType) as e:
        sys.exit(f"Validation Error: {str(e)}")
    
    store_to_database(person)


if __name__ == "__main__":
    main()
This is almost the same code, nothing to explain here. However, we still have some problems that need to be fixed.

Improved Exception Handling
Until now everything looks just fine, but we still have some problems to fix. Let’s say the user will create a Person that has 2 invalid fields: Age and Email. We are first validating the age, then we are validating the email. The main problem is when we display errors to the user. In the current state, we are displaying errors one by one. Which means, if the user enters 5 invalid fields, we will just display the first error. The user corrects his first field, then we display again an error, this time for the next field. This is due to the fact that we are raising many exceptions in our validate method and when an exception is raised the code execution will stop there. We want to display all validation errors once and we still want to raise different kinds of exceptions.

We can do this by using the InvalidPerson exception. We will also split our validate method to have better control over each validation constraint and also for better readability.

validator/person_validator.py
from domain.person import Person
from .interface import IValidator
from .exceptions import InvalidAge
from .exceptions import InvalidEmail
from .exceptions import InvalidPerson


class PersonValidator(IValidator):
    def __init__(self) -> None:
        super().__init__()

    def validate(self, data: Person) -> None:
        ''' Validate all fields '''
        errors = []

        # Validate Age
        try:
            self.validate_age(data.age)
        except InvalidAge as e:
            errors.append(e)
        
        # Validate Email
        try:
            self.validate_email(data.email)
        except InvalidEmail as e:
            errors.append(e)

        # If validatio fails and we have errors, raise them
        if errors:
            raise InvalidPerson("Invalid Person", errors)

    def validate_age(self, age: int) -> None:
        ''' Age validation '''

        if age < 15 or age > 100:
            raise InvalidAge("Age needs to be between 10 and 100")
    
    def validate_email(self, email: str) -> None:
        ''' Email validation '''

        if "@" not in email or "." not in email:
            raise InvalidEmail("Email is not valid")
Now, this Validator looks very clear. You are raising specific Exceptions, you have specific validation methods, everything is crystal clear. However, note that in the main validate() method now we are throwing more information, like past exceptions (InvalidEmail, InvalidAge) and also a message that the person is invalid. This will give us a lot more information about what is going on and a lot more control.

main.py
from domain.person import Person
from validator.person_validator import PersonValidator
from validator.exceptions import InvalidPerson
import sys


def store_to_database(person: Person) -> None:
    """ Saves the person to database """
    print(f"{person.first_name} {person.last_name} stored to database!")


def main():
    validator = PersonValidator()

    person = Person(
        first_name="John",
        last_name="Doe",
        age=240,
        email="johndoegmail.com"
    )

    try:
        validator.validate(person)
    except InvalidPerson as e:
        for exception in e.exceptions:
            print(str(exception.message))
        
        sys.exit(f"Validation Error: {str(e.message)}\n")
    
    store_to_database(person)


if __name__ == "__main__":
    main()
Output:

Age needs to be between 10 and 100
Email is not valid
Validation Error: Invalid Person
And this is how you can use this. This is what we want. We want to validate the entire object and show all the errors to the user in a clear manner.

Other validator examples — Data Validators
So, from here, it’s just a matter of imagination what we can achieve using the validator pattern. To keep the code examples shorter, I will use booleans instead of Exceptions. We can create simple Validators, not only for objects, but just for data.

Take the following code as example:

class LengthValidator(IValidator):
    def __init__(self, min_length, max_length):
        self.min_length = min_length
        self.max_length = max_length

    def validate(self, data):
        # OR RAISE EXCEPTION
        return self.min_length <= len(data) <= self.max_length

class NumericValidator(IValidator):
    def validate(self, data):
        # OR RAISE EXCEPTION
        return data.isdigit()

data = "12345"
length_validator = LengthValidator(5, 10)
numeric_validator = NumericValidator()

if length_validator.validate(data) and numeric_validator.validate(data):
    print("Data is valid!")
else:
    print("Data is invalid!")
This is a code example of a LengthValidator and a NumericValidator, what I want you to look at is that we can also create custom validators with constraints defined in our constructor.

Creating a Validation System
By now you should have a solid understanding of the Validator Pattern and from now on it’s just fun. To keep the examples simple and short, I will use Booleans instead of Exceptions. If you want to create great validation systems, I will suggest you to still use Exceptions or a mix between Exceptions and Booleans.

Description
This will be a very simple Validation System where we can create Rules that will define how our data should be validated. We will start with a simple abstract Rule class which will define our first rule, LengthRule, that will validate our data based on Length (needs to be between min and max). Then we will make a ConditionalRule. Basically this is a Rule that will be applied only if a certain condition is met. We will give functions as conditions. If it sounds overwhelming, keep reading and you will see that it’s nothing complicated.

Project Structure
├── conditions.py
├── main.py
├── rules
|   ├── __init__.py
│   ├── conditional_rule.py
│   ├── length_rule.py
│   └── rule.py
└── validator.py
rules/rule.py
from abc import ABC, abstractmethod

class Rule(ABC):
    """ A basic Rule """

    @abstractmethod
    def apply(self, value: str, context: str = None) -> bool:
        pass
A basic abstract class for a Rule.

rules/length_rule.py
from .rule import Rule


class LengthRule(Rule):
    """ A length Rule """

    def __init__(self, min_length: int, max_length: int) -> None:
        self.min_length = min_length
        self.max_length = max_length


    def apply(self, value: str, context: str = None) -> bool:
        return self.min_length <= len(value) <= self.max_length
This is the LengthRule that checks if the value length is between certain values.

rules/conditional_rule.py
from typing import Callable, Any
from .rule import Rule


class ConditionalRule(Rule):
    """ A conditional Rule """

    def __init__(self, condition: Callable[[Any], bool] , rule: Rule) -> None:
        self.condition = condition
        self.rule = rule


    def apply(self, value: str, context=None) -> bool:
        if self.condition(context):
            return self.rule.apply(value, context)

        return True
This is a ConditionalRule that applies a Rule based on a condition. The condition is a function that checks what the user is doing based on the context provided. If you don’t understand yet, keep reading, it will make sense in a few moments. This is just an example of what kind of stuff you can do using Validator Pattern and imagination.

conditions.py
def is_registration_context(context: str) -> bool:
    """
        A condition for our ConditionalRule that checks
        if the user context is registration
    """

    return context == 'registration'
This is our condition to see if the user is on the registration page or not.

validator.py
from rules.rule import Rule


class Validator:
    """ A simple validator, that applies all the rules """

    def __init__(self):
        self.rules = []

    def add_rule(self, rule: Rule) -> None:
        self.rules.append(rule)

    def validate(self, value: str, context: str = None) -> bool:
        return all(rule.apply(value, context) for rule in self.rules)
This is our Validator and the methods are very clear and simple.

add_rule() — Adds a new rule to our validator.

validate() — Applies all rules to our data.

main.py
from rules.length_rule import LengthRule
from rules.conditional_rule import ConditionalRule
from conditions import is_registration_context
from validator import Validator


def main() -> None:
    # Create the Validator
    validator = Validator()

    # Create a simple Length Rule
    validator.add_rule(
        LengthRule(
            min_length = 5,
            max_length = 7
        )
    )
    
    # Create a Conditional Rule
    validator.add_rule(
        ConditionalRule(
            condition = is_registration_context,
            rule = LengthRule(
                min_length = 7,
                max_length = 12
            )
        )
    )

    print(
        validator.validate(value="abcdef", context="login")
    ) # Output: True
    
    print(
        validator.validate(value="abcdef", context="registration")
    ) # Output: False


if __name__ == "__main__":
    main()
This is where all comes to place and I hope that by now everything makes sense. We have two LengthRule in our validator, one checks if the data is between 5 and 7 characters no matter what the context is and the second is a ConditionalRule that has as condition is_registration_context and checks if the data is between 7 and 12 characters only if the context is “registration”. Be careful, the validator applies both rules the ConditionalRule and the LengthRule!