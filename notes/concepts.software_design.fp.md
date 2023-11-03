---
id: 7lf9c1tjqqrcklg8g0n0obb
title: functional_programming
desc: ''
updated: 1697312365311
created: 1697308522576
---

https://stephendavidwilliams.com/functional-programming-in-data-engineering-with-python-part-1

## Functional Programming

### What is functional programming?🎛️

- Functional programming is a declarative type of programming used to build bug-resistant programs and applications through the use of functions.
- A computing paradigm that emphasizes the **use of pure functions and immutable data structures** for mitigating side effects instead of specifying the steps on how to perform tasks (imperative programming).

### What is a function?🔄

- A function is an object that turns inputs into outputs.

- Functions can be:
  - An input (parameter for another function)
  - An output (result from another function)

- Functions:
  - are treated as first-class citizens (first-class functions)
  - can be passed into other functions as arguments or returned from other functions (higher-order functions)
  - can be connected with other functions to form new ones (function composition)
  - should be designed to work with other functions (reusability)

### Core principles of functional programming🌟

- [[concepts.software_design.fp.core.monoids]]
- [[concepts.software_design.fp.core.immutability]]
- [[concepts.software_design.fp.core.recursion]]
- [[concepts.software_design.fp.core.function_composition]]
- dependency injection
- currying
- referential transparency
- lazy evaluations





## Dependency injections💉
A dependency injection occurs when a resource or behaviour is passed into a function instead of being hard-coded into one.

Examples
Without dependency injection🚫
Let’s see which football team in the Premier League our program believes is the best:

COPY

COPY
team_name = 'Manchester United'

def display_message():
    print(f'{team_name} is the best team in the Premier League!')

display_message()
>>> Manchester United is the best team in the Premier League

This code contradicts the dependency injection rule because the function’s internal operations are coupled to the team_name global variable located outside the function. So although the team_name variable is a dependency for the function in this context, it isn’t “injected” into it via an input parameter.

With dependency injection✔️

COPY

COPY
def display_message(team_name):
    print(f'{team_name} is the best team in the Premier League!')

team_name = 'Manchester United'
display_message(team_name)

COPY

COPY

### Output ###

# Manchester United is the best team in the Premier League

This method is flexible enough to have any team names passed into it, so if another team ends up performing better in the Premier League than Manchester United (highly unlikely), then we can easily pass in their names without interfering with the function’s internal code.

## Currying🍛
Currying is turning a function that takes in multiple arguments into a sequence of nested functions that each take one argument.

By converting a multiple-argument function to a hierarchy of single-argument functions, we can create more modular and reusable code that makes it easier to create partial functions for different use cases.

Examples
Without currying🚫

def make_breakfast(toasts, sausages, eggs):
  return f"Breakfast made with {toasts} pieces of toasts, {sausages} sausages and {eggs} scrambled eggs"

my_breakfast = make_breakfast(3, 4, 3)
print(my_breakfast)
>>> Breakfast made with 3 pieces of toasts, 4 sausages and 3 scrambled eggs

This example isn’t considered to be currying because the make_breakfast function takes 3 input arguments at once.

With currying✔️

COPY

COPY
def make_breakfast(toasts):
    def add_sausages(sausages):
        def add_eggs(eggs):
            return f"Breakfast made with {toasts} pieces of toasts, {sausages} sausages and {eggs} scrambled eggs"
        return add_eggs
    return add_sausages

my_breakfast = make_breakfast(3)(4)(3)
print(my_breakfast)

COPY

COPY

### Output ###

# Breakfast made with 3 pieces of toasts, 4 sausages and 3 scrambled egg

By splitting the make_breakfast function into single-argument functions nested inside it, it’s easier to see the inputs required to make the function work. We can also reuse the make_breakfast function to create partial functions for other bespoke uses.

COPY

COPY
def make_breakfast(toasts):
    def add_sausages(sausages):
        def add_eggs(eggs):
            return f"Breakfast made with {toasts} pieces of toasts, {sausages} sausages and {eggs} scrambled eggs"
        return add_eggs
    return add_sausages

make_breakfast_with_2_toasts = make_breakfast(2)
my_breakfast = make_breakfast_with_2_toasts(6)(4)

print(my_breakfast)

COPY

COPY

### Output ###

# Breakfast made with 2 pieces of toasts, 6 sausages and 4 scrambled eggs

In this example, we created a partial function, make_breakfast_with_2_toasts - by reusing make_breakfast function, which sets the number of toasts to 2, while still specifying any amount of sausages and eggs we want to be included in our breakfast. This demonstrates the reusability and flexibility of curried functions for various use cases.

Referential transparency🔍
Referential transparency is when the result of a function can be swapped with its inputs without any changes in the behaviour. The inputs expressed must always return a specific output under any circumstance.

A good example of referential transparency is the use of a dictionary. In the Cambridge Dictionary, the term courage is defined as “ the ability to control your fear in a dangerous or difficult situation”. This definition will always be mapped to courage if we keep checking it in this dictionary.

Examples
Without referential transparency🚫
Here’s a code example of what life looks like without referential transparency:

COPY

COPY

# Set discount factor

discount_factor = 0.5

# Define discount function

def apply_discount(item, price, location):
    global discount_factor
    if location == "USA" and item == "shoes":
        price *= discount_factor
        discount_factor += 0.1
    return price

# Add items

item = "shoes"
price = 30
location = "USA"

# Display results

discounted_price_1 = apply_discount(item, price, location)
discounted_price_2 = apply_discount(item, price, location)

print(f'Discounted price 1: {discounted_price_1} ')
print(f'Discounted price 2: {discounted_price_2} ')

COPY

COPY

### Output ###

# Discounted price 1: 15.0

# Discounted price 2: 18.0

This violates referential transparency because the apply_discount function does not primarily depend on all its inputs - it doesn’t rely solely on location and item inputs, but on the discount_factor global variable’s value. This means calling the function with the same input value may generate different results depending on the value of the discount_factor variable.

The discount_factor value increases in value by 0.1 each time the apply_discount function is executed, which means the same inputs for the function return different results every time, and thus the output for the discounted prices are 15 and 18 respectively.

With referential transparency✔️
Here’s a refactored version that satisfies referential transparency this time:

COPY

COPY

# Set discount factor

discount_factor = 0.5

# Define discount function

def apply_discount(item, price, location, discount_factor):
    if location == "USA" and item == "shoes":
        price *= discount_factor
    return price

# Add items

item = "shoes"
price = 30
location = "USA"

# Display results

discounted_price_1 = apply_discount(item, price, location, discount_factor)
discounted_price_2 = apply_discount(item, price, location, discount_factor)

print(f'Discounted price 1: {discounted_price_1} ')
print(f'Discounted price 2: {discounted_price_2} ')

COPY

COPY

### Output ###

# Discounted price 1: 15.0

# Discounted price 2: 15.0

The apply_discount function now depends on all inputs instead of any external/global variables influencing its internal operations. The discount_factor is now an input argument itself to guarantee the function relies on the same input parameters to return the same results every time it is executed.

Lazy evaluation🥱
Lazy evaluation is a technique used to execute functions only when they are required/called, not when they are created.

Examples
Eager evaluation🚫
The opposite of lazy evaluation is eager evaluation, which is when a function is “eager” to be executed - in other words, it runs the moment it is created.

A code example would look like this:

COPY

COPY

# Create a generator that triples the numbers in a list

def triple_numbers(numbers):
    return [f'{number} * 3 = {number * 3}' for number in numbers]

# Create the list of numbers

list_of_numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9 , 10]
tripled_numbers = triple_numbers(list_of_numbers)

# Display the results

for eager_result in tripled_numbers:
    print(eager_result)
>>> 1 * 3 = 3
>>> 2 * 3 = 6
>>> 3 * 3 = 9
>>> 4 * 3 = 12
>>> 5 * 3 = 15
>>> 6 * 3 = 18
>>> 7 * 3 = 21
>>> 8 * 3 = 24
>>> 9 * 3 = 27
>>> 10 * 3 = 30

This would not qualify for lazy evaluation because the triple_numbers uses a list comprehension to print the computed results once calculations are completed.

Lazy evaluation✔️
This is what the lazy evaluation version looks like:

COPY

COPY

# Create a generator that triples the numbers in a list

def triple_numbers(numbers):
    for number in numbers:
        yield f'{number} *3 = {number* 3}'

# Create the list of numbers

list_of_numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9 , 10]
tripled_numbers = triple_numbers(list_of_numbers)

# Display the results

for lazy_result in tripled_numbers:
    print(lazy_result)
# 1 * 3 = 3
# 2 * 3 = 6
# 3 * 3 = 9
# 4 * 3 = 12
# 5 * 3 = 15
# 6 * 3 = 18
# 7 * 3 = 21
# 8 * 3 = 24
# 9 * 3 = 27
# 10 * 3 = 30

By replacing the list comprehension with a yield generator, the triple_numbers generator is only executed once it is needed by the tripled_numbers variable.

Types of functions used in functional programming
Deterministic functions🎯
A deterministic function takes the same input and returns the same output each time. Because they return the same result each time it receives a specific input argument, we can rely on the results of these types of functions, making them reliable.

Deterministic functions are like calculators - if you add 1 + 1 into a calculator, the answer returned should always be 2.

Now, let's look at the code examples:

Examples
Non-deterministic functions🎲🚫
A function is non-deterministic if it doesn’t return the same output every time you pass the same input into it. In other words, the output is always random and unpredictable even when you feed it the same input arguments.

Here is an example of a non-deterministic function:

COPY

COPY
import random

# Create a function that cleans data

def clean_data(data):
    return data.strip().lower()

# Add the input number to a random number between -100 and 100

def add_random_number(data):
    random_number = random.uniform(-100, 100)
    return data + random_number

# Run the operations

data                =   "  100.00   "
transformed_data    =   float(clean_data(data))
random_result       =   add_random_number(transformed_data)

# Display results

print(random_result)
This code defines a clean_data function that removes whitespace from the data and converts the input data to lowercase. Then the add_random_number function adds a different random number to the input provided every time it is executed, therefore making the operation a non-deterministic one.

Running this 3 times returned these results…

COPY

COPY

### Output ###

# 107.06291086940836

# 125.20529145203881

# 5.629407901961244

The problem with them is obvious - they often give us unreliable outputs. There are use cases where this is necessary (like experimentation, testing etc). But for production-grade applications that require predictable outcomes, these functions fall short of the mark.

Deterministic functions✔️

COPY

COPY

# Clean the data

def clean_data(data):
    return data.strip().lower()

# Add the input number to a fixed number

def add_fixed_numbers(transformed_data, fixed_number):
    return transformed_data + fixed_number

# Run the operations

data                =   "  100.00   "
transformed_data    =   float(clean_data(data))
fixed_result        =   add_fixed_numbers(transformed_data, 50)

# Display results

print(fixed_result)
Running this code three times returns these outputs:

COPY

COPY

### Output ###

# 150.0

# 150.0

# 150.0

By using the add_fixed_numbers instead of the add_random_numbers function, the number added to the input number is always the same, making the add_fixed_numbers operation a deterministic one.

Pure functions👼
A pure function is a deterministic function that takes the same input and returns the same output each time it is executed without any side effects. No matter how many times it runs, it consistently returns the same result with the same input argument(s).

Not only does it consistently return the same results if the same input arguments are provided, but also has no side effects, meaning it doesn’t alter any external states (it does not change the values outside of it).

See above for the code example that satisfies deterministic functions of an example of a pure function. The add_fixed_numbers function is a pure function that always returns the same value for the same input arguments it’s given.

Higher-order functions🔝
A higher-order function can either take a function as an input or return a function as an output.

See the code example that satisfies function composition as an example of a custom higher-order function. The compose_data_cleaning_functions function is a higher-order function that takes in multiple functions as input parameters.

In-built higher-order functions include:

map

filter

reduce

Map🗺️
The map function takes a function and applies it to every item in an iterable (e.g. lists, tuples).

Here’s a code example of applying map on a list:

COPY

COPY
convert_to_caps     =   lambda name_in_lowercase: name_in_lowercase.title()
old_list_of_names   =   ['amy', 'chris','rachel' ,'terry', 'abraham']

new_list_of_names   =   list(map(convert_to_caps, old_list_of_names))
print(new_list_of_names)

COPY

COPY

### Output ###

# ['Amy', 'Chris', 'Rachel', 'Terry', 'Abraham']

The map function takes the convert_to_caps function as the first argument and applies it to each name in the iterable object named old_list_of_names to convert all the names from lowercase to uppercase.

Here’s another example of applying map on a tuple this time:

COPY

COPY

# Define the data transformation function

def transform_employee_data(employee):
    name, age, department = employee
    return (name.title(), age, department.upper())

# Create the employee data

employee_data  = (
    ('john smith', 32, 'sales'),
    ('amy holloway', 27, 'business intelligence'),
    ('ryan bakerwood', 45, 'operations')
    )

# Apply the transformation

transformed_employee_data = tuple(map(transform_employee_data, employee_data))
print(transformed_employee_data)

COPY

COPY

### Output ###

# (('John Smith', 32, 'SALES')

# ('Amy Holloway', 27, 'BUSINESS INTELLIGENCE')

# ('Ryan Bakerwood', 45, 'OPERATIONS'))

The map function takes transform_employee_data function and applies it to each employee in the employee_data object, converting the first letter of each name from lowercase to uppercase, and raising each department name to uppercase.

Filter🔍
The filter operation is used to select (or remove) a subset of data from an iterable. It takes a function with a condition, applies it to the iterable and returns the iterable’s elements that meet the function’s condition.

Here’s a list example:

COPY

COPY
list_of_numbers             =   [-2, 6, -24, -928, 13, 83, 401]
only_positive_numbers       =   lambda positive_number: positive_number > 0
positive_numbers            =   list(filter(only_positive_numbers, list_of_numbers))

print(positive_numbers)

COPY

COPY

### Output ###

# [6, 13, 83, 401]

This code employs the filter function, which filters out the positive numbers from a given list of random numbers, list_of_numbers. This is accomplished by using a lambda function named only_positive_numbers, which evaluates whether a number is greater than 0. The filter operation applies the lambda function to each element in the list_of_numbers and only retains the numbers that satisfy the condition. The resulting numbers are then stored in the positive_numbers variable as a new list.

Now here’s a tuple example:

COPY

COPY
def filter_country_data(country):
    country_name, population_in_millions = country
    return population_in_millions >= 500

# Create the country data

country_data_in_millions  = (
('China', 1402),
('India', 1366),
('United States', 329),
('Indonesia', 270),
('Brazil', 212),
('Pakistan', 205),
('Nigeria', 201),
('Bangladesh', 168),
('Russia', 144),
('Japan', 127))

# Apply the filter

filtered_country_data = tuple(filter(filter_country_data, country_data_in_millions))
print(filtered_country_data)

COPY

COPY

### Output ###

# (('China', 1402), ('India', 1366))

This code uses population data by country to filter out any country containing a population less than 250 million by using the filter operation to apply the filter_country_data operation to each tuple in the country_data_in_millions iterable.

Reduce➗
The reduce function takes a function and applies it to every item in an iterable to reduce it to a single cumulative value. This is done by applying the function to the first two elements in the iterable, repeating the process until only one cumulative value remains.

Let’s observe a string-based example:

COPY

COPY
from functools import reduce

random_strings = ("I", "love", "pizza", "and", "orange", "juice", "!")
concat_operation = lambda x, y: x + " " + y
sentence = reduce(concat_operation, random_strings)
print(sentence)

COPY

COPY

### Output ###

# I love pizza and orange juice

Here we use the reduce function to apply the concat_operation to the first two elements in the tuple of random_strings. The operation concatenates the elements within the iterable and separates them by a space. The result is then stored in the sentence variable.

Now let’s see a numerical example:

COPY

COPY
from functools import reduce

def calculate_net_profit(net_profit, transaction):
    gross_profit, expenses, tax = transaction
    return net_profit + gross_profit - expenses - tax

list_of_transactions = [(2000, 500, 300), (3000, 750, 450), (1000, 250, 150), (5000, 1250, 750)]
net_profit = reduce(calculate_net_profit, list_of_transactions, 1000)

print(net_profit)

COPY

COPY

### Output ###

# 7600

The reduce function is used to calculate the net profit using the gross_profit, expenses and tax variables. The custom calculate_net_profit function takes the net_profit and transaction values unpack the transaction tuple into the gross_profit, expenses and tax variables then calculate the net profit by adding the net_profit and gross_profit together to then subtract the expenses and tax from the final figure.

The calculate_net_profit function is applied to each element in the list_of_transactions variable, with the net_profit set to 1000 (just for demonstrative purposes).

First-order functions🔢🔡
A first-order function is a function that operates on simple data types like numbers, lists and strings, and doesn’t take arguments as functions or return one as output. They’re basically what many consider to be normal functions.

Higher-order functions can do the same, except they can take functions as arguments, return them as outputs, or both.

Examples of in-built first-order functions in Python are:

zip

sorted

enumerate

any

all

Zip🤐
The zip function joins elements that share the same index position from different iterables into one new iterable in tuple format.

COPY

COPY
employees       =   ['Brian Jackson', 'Melissa Hammersmith', 'Connor Shaw']
salaries        =   [35000, 45000, 55000]

employee_data   =   list(zip(employees, salaries))
bonuses         =   [(employee, salary * 0.1) for employee, salary in employee_data]

print(bonuses)

COPY

COPY

### Output ###

# [('Brian Jackson', 3500.0), ('Melissa Hammersmith', 4500.0), ('Connor Shaw', 5500.0)]

This example demonstrates the use of the zip function to combine the employees and salaries list based on their index positions, which creates a new list, employee_data. A list comprehension is used to apply the 10% bonus to each employee’s salary.

Using the zip function simplifies the process of combining multiple lists and making the code more readable and easier to understand.

Sorted📚
The sorted function takes an iterable as an input and creates a new list sorted in ascending order. Although any iterable (mutable or immutable) can be passed into the sorted function, the output is always in list format.

Note: The sorted function becomes a higher-order function once the key function is taken as an input argument. Here’s an example of this:

COPY

COPY
action_movies   =   (
        {'title':'Inception', 'year': 2010},
        {'title':'Rush Hour', 'year': 1998},
        {'title':'Avengers: Endgame', 'year': 2019},
        {'title':'Bad Boyz', 'year': 1995},
        {'title':'John Wick', 'year': 2014}
    )

year_key        =   lambda x: x["year"]
sorted_movies   =   sorted(action_movies, key=year_key)

print(sorted_movies)

COPY

COPY

### Output ###

# [{'title': 'Bad Boyz', 'year': 1995}

# {'title': 'Rush Hour', 'year': 1998}

# {'title': 'Inception', 'year': 2010}

# {'title': 'John Wick', 'year': 2014}

# {'title': 'Avengers: Endgame', 'year': 2019}]

This code uses the sorted function to order a list of dictionaries containing different action films and their release dates in ascending order. The key argument takes in the lambda function, year_key, which extracts each year from the dictionaries.

Enumerate🧮
The enumerate operation supplies each value in an iterable with an index. This is useful for monitoring the number of iterations in a loop operation.

COPY

COPY
sales = [2000, 1500, 4600, 39000, 6500, 800]
sorted_sales = sorted(sales, reverse=True)

for rank, amount in enumerate(sorted_sales, start=1):
    print(f'Rank {rank}: ${amount} ')

COPY

COPY

### Output ###

# Rank 1: $39000

# Rank 2: $6500

# Rank 3: $4600

# Rank 4: $2000

# Rank 5: $1500

# Rank 6: $800

The example here uses enumerate to iterate through a list of ordered sale amounts, sorted_sales, with their corresponding index values next to each amount. This saves us the hassle of manually incrementing using a counter variable.

So we’re still running a normal for loop but with an index added to each value to see where they are ranked in the iteration.

Any❓
The any function is an operation applied to an iterable to check whether at least one element in the iterable is true. If there is at least one element in the iterable that is true, the operation will return True.

It will return False if the iterable is empty or all the elements in the iterable are false.

COPY

COPY
inventory  = [
    {'name': 'notebooks', 'quantity': 120},
    {'name': 'pencils', 'quantity': 140},
    {'name': 'highlighters', 'quantity': 233},
    {'name': 'sticky-notes', 'quantity': 56},
    ]

minimum_stock_required = 100

any_low_stock_in_inventory = any(item["quantity"] <  minimum_stock_required for item in inventory)

print(any_low_stock_in_inventory)

COPY

COPY

### Output ###

# True

The inventory variable is a list of dictionaries that list stock items and their quantities. The any function checks each stock item to see whether their quantity is less than the minimum_stock_required. If the program identifies any item lower than the minimum threshold defined in minimum_stock_required, it will return False, indicating some items required restocking.

All✅
The all function is another in-built function applied to an iterable that returns True only if all elements in the iterable are true or the iterable is empty.

This will return False if any element in the iterable is false.

COPY

COPY
employees_trained = (
    {'name': 'Shannon', 'passed': True},
    {'name': 'Rhys', 'passed': True},
    {'name': 'Jimmy', 'passed': False},
    {'name': 'Emma', 'passed': True},
    {'name': 'Ben', 'passed': True},

)

all_employees_passed = all(employee['passed'] for employee in employees_trained)
print(all_employees_passed)

COPY

COPY

### Output ###

# False

The employees_trained is a tuple of employee names and a flag indicating whether they passed/failed the course. The all function iterates through the tuple to check whether each employee passed the course (i.e. returns True if all the items in the employees_trained iterable returns True).

In this case, it returns False because the program identified at least one employee who didn’t pass the course in this round.

Closures🔐
A closure is a function stored or nested in another function that remembers all the hard-coded values in the outside function even after the outside function has completed running.

See the code example that satisfies function composition as an example of a closure. The compose function is a nested function that captures the functions passed into its outside function, compose_data_cleaning_functions, and applies each function (f) to the value selected (v) in the reduce operation.

Partially-applied functions🎛️
A function is “applied”, or considered a total function if it’s given all of its compulsory arguments in one go. A function is “partially-applied” if it is only given a subset of its compulsory arguments with the hope of getting the rest later on via a new function.

A partially-applied function takes some of its mandatory parameters and then creates a new function to take the remaining input parameters on its behalf.

Examples
Total function🚫
Here’s what a total function (or applied function) can look like:

COPY

COPY
def transform_data(data, strip_whitespace, use_lowercase):
    if strip_whitespace:
        data = data.strip()
    if use_lowercase:
        data = data.lower()
    return data

dummy_data = "  My FRIENDS love to TAKE WALKS in the park.       "
clean_data = transform_data(dummy_data, True, True)

print(clean_data)

COPY

COPY

### Output ###

# my friends love to take walks in the park

All the compulsory arguments have been supplied to the transform_data function once it’s called, which makes it a total function.

Partially-applied function✔️

COPY

COPY
from functools import partial

def transform_data(data, strip_whitespace, use_lowercase):
    if strip_whitespace:
        data = data.strip()
    if use_lowercase:
        data = data.lower()
    return data

dummy_data = "  My FRIENDS love to TAKE WALKS in the park. "
clean_data = partial(transform_data, dummy_data, strip_whitespace=True, use_lowercase=True)
final_data = clean_data()

print(final_data)

COPY

COPY

### Output ###

# my friends love to take walks in the park

Partial functions🧩
A partial function is a function built from another function but already has some input arguments filled. Partial functions are useful for extending the functionality of its base function created for other bespoke use cases.

Note: Partial applications are not to be confused with currying.

Examples
Without partial functions🚫

COPY

COPY

# Create function to calculate profit

def calculate_profit(revenue, cost, tax_rate):
    return (revenue - cost) * (1 - tax_rate)

# Specify financial constants

revenue     =   1_000_000
cost        =   50_000
tax_rate    =   0.2

# Calculate and display profit

profit      =   calculate_profit(revenue, cost, tax_rate)
print(profit)

COPY

COPY

### Output ###

# 760000.0

This code example forced us to add the three input parameters (revenue, cost and tax_rate) directly into the calculate_profit function, which means this isn’t partial.

With partial functions✔️

COPY

COPY
from functools import partial

# Create function to calculate profit

def calculate_profit(revenue, cost, tax_rate):
    return (revenue - cost) * (1 - tax_rate)

# Specify financial constants

revenue     =   1_000_000
cost        =   50_000
tax_rate    =   0.2

# Create partial function

calculate_profit_with_tax = partial(calculate_profit, tax_rate=tax_rate)

# Calculate and display profit

profit      =   calculate_profit_with_tax(revenue, cost)
print(profit)

COPY

COPY

### Output ###

# 760000.0

By using the partial function from the functools module, we can create a new function called calculate_profit_with_tax that already fills the tax_rate input parameter, so that all that needs to be added to the new function are the revenue and cost input parameters to the calculate_profit_with_tax function.
