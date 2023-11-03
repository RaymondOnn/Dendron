---
id: 3wuw6656277peti9xxhmg88
title: error_handling
desc: ''
updated: 1697279887547
created: 1691997473790
---



- Medium article: https://levelup.gitconnected.com/python-the-unacceptable-except-fd633c85c3ae

Resource: https://www.youtube.com/watch?v=8kTlzR4HhWo&ab_channel=MiguelGrinberg

## Types of Errors

### Recoverable and Unrecoverable Errors

- Can you recover from an error?
  - if the function can continue in spite of the error: **recoverable**
  - if the function needs to stop, then the error is **unrecoverable**
- An unrecoverable error can become recoverable at a higher scope level

### 4 possible cases

#### Recoverable Errors

1. If your function generates a recoverable error, then recover it and keep going

   ``` py
   def add_song(song):
        if song.year is None:
            song.year = 'Unknown'
   ```

2. if your function calls another function that triggers a recoverable error, then use try...except block to catch the error, recover it and keep going

   ``` py
   def export(filename):
    try:
        os.remove(filename)
    except OSError:
        pass

   ```

#### Unrecoverable Errors

3. If your function generates an unrecoverable error, raise an exception to alert the higher scope levels
   
   ```py
   def validate_cusomer(customer):
        if customer.name is None or customer.name == '':
            raise ValidationError('Cuatomer has no name.')
   ```

1. if your function calls another function that triggers an unrecoverable error, just call the function and let it error if it wants to!
   
   ``` py
   def save_customer_to_db(customer):
        validate_customer(customer)
        write_customer(customer)
   ```

## Implementation

```py
@app.route('/songs/<id>', methods=['PUT'])
def new_song(id):
    db.session.add(song)
    db.session.commit()
    return "", 204
```

- The error will **bubble up** until it gets to Flask, which can recover it
- Flask will write the error;s stack track to the log (or start the debugger)
- ...then it will do cleanup (including a db session rollback)
- ...then it will send a code 500 error to the client

``` py
def save_customer_to_db(customer):
    validate_customer(customer)
    write_customer(customer)

if __name__ = '__main__':
    args = parser.parse_args()
    try:
        save_customer_to_db(args.customer)
    except (ValidationError, IOError) as e:
        logger.exception('Error: ' + str(e))

```

## Handling errors with `merry`
- [`merry` Github repo](https://github.com/miguelgrinberg/merry/)

``` py
# error.py
from merry import Merry
merry = Merry()

@merry._except(ValidationError, IOError)
def handle_error(e):
    print('Error: ' + str(e))


@merry._except(Exception)
def catch_all_error(Exception):
    print('Unexpected errpr: ' + str(e))    

# main.py
from errors import merry

@merry._try
def save_customer_to_db(customer):
    validate_customer(customer)
    write_customer(customer)

if __name__ = '__main__':
    args = parser.parse_args()
    save_customer_to_db(args.customer)
```

### Debug Mode with `merry`

- Debug mode is turned on / off in the constructor

```py
# debug=True suspends exception handling and bubbles exceptions up
merry = Merry(debug=True)
```

### Production Mode with `merry`

- `merry` writes stack traces for al exceptions to a logger object
- A catch-all handler easily prevents application crashes
  
```py
@merry._except(Exception)
def catch_all(Exception):
    # recover unexpected errprs here 
```

## Summary

- if an error is recoverable, recover it and move on
- if an error is unrecoverable, let it bubble up until it becomes recoverable
- During development, let errors crash your application
- During product, log all errors and recover them as best as you can
- Put error handles at the highers scope levels
- Keep error handles away from your application logic whenever possible
