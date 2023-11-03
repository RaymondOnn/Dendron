---
id: 3sn6aq18csc0jgrqyfzdzyt
title: immutability
desc: ''
updated: 1697311336692
created: 1697310951307
---


## Immutability🧱

### What is immutability

- An object is immutable if it cannot be changed or altered under any circumstance.
- In functional programming, immutability is when an object’s state is unable to change once it has been created or initialized.
- So under this form of programming, we only create new objects instead of modifying existing ones.

### Why immutability

- Using immutable objects helps to avoid unintended side effects like changing the state of objects outside the functions used.
- Let’s take a bank statement as an example -
  - transactions cannot be modified or deleted once they are recorded onto a bank statement
  - you can only add new transactions to record changes to existing ones in the form of corrections, adjustments or reversals.

### Types of data

#### Mutable data structures🔀

- Common examples of mutable data structures include:
  - lists
  - dictionaries
  - sets

#### Immutable data structures🔒

- Common examples of immutable data structures include:
  - strings
  - tuples
  - fronzensets

### Examples

#### Mutable operation
Here is an example of a function that deals with mutable data type:

``` py
# Create function for standardizing a sequence of dates
def change_date_formats(dates):
    for i, date in enumerate(dates):
        dates[i] = date.replace('-', '/')
    return dates

# Run the operations
list_of_dates = ['2023-01-01', '2023-02-01', '2023-03-01', '2023-04-01']
transformed_dates = change_date_formats(list_of_dates)

# Display results
print(f'Old dates: {list_of_dates} ')
print(f'New dates: {transformed_dates} ')
>>> Old dates: ['2023/01/01', '2023/02/01', '2023/03/01', '2023/04/01']
>>> New dates: ['2023/01/01', '2023/02/01', '2023/03/01', '2023/04/01']
```

The old dates have been overwritten with the new results by the change_date_formats function.

#### Immutable operation
Here is an approach that doesn't modify the existing list (remember, it's recommended you use immutable data structures in general, especially for parallel computing activities):

```py
# Create function for standardizing a sequence of dates
def change_date_formats(dates):
    new_dates = [date.replace('-', '/') for date in dates]
    return new_dates

# Run the operations
list_of_dates = ['2023-01-01', '2023-02-01', '2023-03-01', '2023-04-01']
transformed_dates = change_date_formats(list_of_dates)

# Display results
print(f'Old dates: {list_of_dates} ')
print(f'New dates: {transformed_dates} ')\
>>> Old dates: ['2023-01-01', '2023-02-01', '2023-03-01', '2023-04-01']
>>> New dates: ['2023/01/01', '2023/02/01', '2023/03/01', '2023/04/01']
```

The change_date_formats function uses a list comprehension to perform the transformation job on each date while recreating them as new dates once the - characters are replaced with /.

However, although our approach is correct, our new output is still in a list format and can be accidentally modified or take up more memory space than we need. The severity of this becomes more apparent once the data grows larger.

The best approach is to use an immutable data type, like tuples:

``` py
# Create function for standardizing a sequence of dates
def change_date_formats(dates):
    new_dates = tuple(date.replace('-', '/') for date in dates)
    return new_dates

# Run the operations
tuples_of_dates = ('2023-01-01', '2023-02-01', '2023-03-01', '2023-04-01')
transformed_dates = change_date_formats(tuples_of_dates)

# Display results
print(f'Old dates: {tuples_of_dates} ')
print(f'New dates: {transformed_dates} ')
>>> Old dates: ('2023-01-01', '2023-02-01', '2023-03-01', '2023-04-01')
>>> New dates: ('2023/01/01', '2023/02/01', '2023/03/01', '2023/04/01')
```

Now that the results are in tuples, we can expect our program to process the dates more efficiently and the dates are protected from manual modification or corruption by the user or the program.
