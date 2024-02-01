---
id: cj019byqr3ll2bdunw6my7p
title: class
desc: ''
updated: 1704582892224
created: 1704425936045
---

### What is `classmethod()`

-   `classmethod()` is a built-in function in Python that is used to define a method that **operates on the class** instead of the instance of the class.
-   Can be used to manipulate class-level data or perform operations that are not specific to any one instance of the class.

### Syntax

-   The syntax of classmethod() is as follows:
    ```py
    class MyClass:
        @classmethod
        def my_class_method(cls, arg1, arg2, ...):
            # code here
    ```
    -   The `classmethod()` function is applied as a decorator to a method of a class.
    -   The first argument of a class method is always the class itself, represented by the parameter ‘cls’. This allows the method to access and modify class-level data.

### Creating factory methods

-   Can be used to create factory methods that create and return new instances of a class with a specific configuration.

    ```py
    class Person:
        def __init__(self, name, age):
            self.name = name
            self.age = age

        # Acts as a factory that creates instances using birth_year
        @classmethod
        def from_birth_year(cls, name, birth_year):
            age = cls.get_age(birth_year)
            return cls(name, age)

        @staticmethod
        def get_age(birth_year):
            return datetime.date.today().year - birth_year

    person = Person.from_birth_year('John', 1990)
    print(person.name)  # John
    print(person.age)  # 33
    ```

### Creating alternative constructors

-   Can be used to create alternative constructors for a class. This is useful when you want to create instances of a class with different sets of arguments.

    ```py
    class Point:
        def __init__(self, x, y):
            self.x = x
            self.y = y

        @classmethod
        def from_tuple(cls, tuple):
            x, y = tuple
            return cls(x, y)

    point = Point.from_tuple((3, 4))
    print(point.x) # 3
    print(point.y) # 4
    ```

    In this example, the from_tuple() class method acts as an alternative constructor that creates a new Point instance from a tuple of (x, y) values. It returns a new instance of the Point class.

### Managing class-level state

-   Can be used to manage class-level state. This means that you can define a method that modifies or accesses a variable that is shared by all instances of the class.

    ```py
    class Car:
        total_cars_sold = 0

        def __init__(self, make, model):
            self.make = make
            self.model = model
            Car.total_cars_sold += 1

        @classmethod
        def get_total_cars_sold(cls):
            return cls.total_cars_sold

    car1 = Car('Toyota', 'Camry')
    car2 = Car('Honda', 'Civic')

    print(Car.get_total_cars_sold()) # 2
    ```

### Implementing class-level operations

-   Can be used to implement class-level operations, such as sorting or filtering a list of instances of the class.

    ``` py
    class Student:
        all_students = []

        def __init__(self, name, grade):
            self.name = name
            self.grade = grade
            Student.all_students.append(self)

        @classmethod
        def get_highest_grade(cls):
            return max(cls.all_students, key=lambda student: student.grade)

        @classmethod
        def get_lowest_grade(cls):
            return min(cls.all_students, key=lambda student: student.grade)

    student1 = Student('John', 90)
    student2 = Student('Jane', 95)
    student3 = Student('Alice', 80)

    print(Student.get_highest_grade().name) # Jane
    print(Student.get_lowest_grade().name) # Alice
    ```
