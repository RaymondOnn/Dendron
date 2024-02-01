---
id: 0n3n40duzpd08z4o96u5rjy
title: config
desc: ''
updated: 1705557010286
created: 1696700247660
---

## Considerations

### Naive Approach

- One common way to store configuration information is to use global variables.
- The problem with this approach is overwriting variables
- Also, since they are just distinct global variables, you can not iterate over them or collectively perform safety checks on them without introducing more code.

``` py
# Configuration for RandomForestClassifier
N_ESTIMATORS = 100
MAX_DEPTH = 8
N_JOBS = 3

# Configuration for AdaBoostClassifier
N_ESTIMATORS = 50
LEARNING_RATE = 1.0

# Now N_ESTIMATORS has been overwritten
```

### The Better Approach: [[lang.py.basics.classes.type.enums]]

- Enums provide a more elegant solution for storing configuration information.
- Enums are essentially a way to define a set of named constants.

``` py
from enum import Enum

class RandomForest(Enum):
  """An Enum for tracking the configuration of random forest classifiers."""
  N_ESTIMATORS = 100
  MAX_DEPTH = 8
  N_JOBS = 3

class AdaBoost(Enum):
  """An Enum for tracking the configuration of Ada boost classifiers."""
  N_ESTIMATORS = 50
  LEARNING_RATE = 1.0
```

#### Why is it better
- Each parameter (e.g., MAX_DEPTH) is now stored hierarchically within the model they are used for. This makes sure that nothing is overwritten when more configuration code is introduced, avoiding the need for overly long variable names.
- The different parameters used in RandomForestClassifier are now grouped in the RandomForest Enum. Thus they can be iterated over and collectively analyzed for type safety.
- Since Enums are classes, they can have docstrings as I have illustrated above, which helps with documentation

#### Accessing config values
If you now want to access the configuration code further down in the script, you can simply write:

``` py
from sklearn.ensemble import RandomForestClassifier

RandomForestClassifier(
  n_estimators=RandomForest.N_ESTIMATORS.value,
  max_depth=RandomForest.MAX_DEPTH.value,
  n_jobs=RandomForest.N_JOBS.value
)
```

#### Working with Enums
Let’s illustrate some simple features of Python enumerations by looking at the toy example:
```py
from enum import Enum
class HTTPStatusCodes(Enum):
    """An Enum that keeps track of status codes for HTTP(s) requests."""
    OK = 200
    CREATED = 201
    BAD_REQUEST = 400
    NOT_FOUND = 404
    SERVER_ERROR = 500

# To extract the names and values:
print(HTTPStatusCodes.OK.name)
>>> OK

print(HTTPStatusCodes.OK.value)
>>> 200

# Given that a status code value is 404, you can find the status code name
print(HTTPStatusCodes(200).name)
>>> OK

# You can collectively work with the names/values pairs in an Enum by e.g., using the list constructor list():
print(list(HTTPStatusCodes))
>>> [
  <HTTPStatusCodes.OK: 200>, 
  <HTTPStatusCodes.CREATED: 201>, 
  <HTTPStatusCodes.BAD_REQUEST: 400>, 
  <HTTPStatusCodes.NOT_FOUND: 404>,
  <HTTPStatusCodes.SERVER_ERROR: 500>
]


# Finally, you can pickle and unpickle enumerations in Python. To do this, simply use the pickle model as you would with other familiar objects in Python:
from pickle import dumps, loads
print(HTTPStatusCodes is loads(dumps(HTTPStatusCodes)))
>>> True
```
