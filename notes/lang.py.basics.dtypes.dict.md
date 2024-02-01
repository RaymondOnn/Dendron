---
id: mo0cci9jwbrs19m5wh5hgrw
title: dict
desc: ''
updated: 1700931908037
created: 1693289683911
---
https://www.youtube.com/watch?v=u0yr9B3nH8c&ab_channel=Indently

## Dictionary Methods

##### `.values()`

Gets all values, stored within an iterable (list)

##### `.keys()`

Gets all keys, stored within an iterable (list)

##### `.pop(<key>)`

Remove key-value pair from dictionary based on key and returns the removed value  
`KeyError` if key does not exist

##### `.popitem()`

Remove last key-value pair from dictionary  
`KeyError` if there's nothing left in the dictionary

##### `.copy()`

Creates a shallow copy of the dictionary. Can verify using `id(<dict>)`  
Shallow copy only copies the references of the values, instead of the exact values

##### `.get(<key>, <default_value if key not found>)`

Safe way to retrieve values from dictionary.  
Returns None if key not found unless specified

##### `.setdefault(<key>, <default_value>)`
Retrieve values from dictionary.  
Creates key-value pair if key not found

##### `.clear()`
Removes all key-value pairs from dictionary

##### `.fromkeys(<iterable_to_compare_against>, <default_value>)`


##### `.items()`
Gets all key-value pairs, stored within an iterable

##### `.update(<new_dict>)`
same functionality as `old_dict |= new_dict`
updates the dictionary. if clashing keys, data is overwritten by the new_dict


### Operators

``` py
# With clashing keys, the right dictionary has precedence
# merge (|) operator
d = {'spam': 1, 'eggs': 2, 'cheese': 3}
e = {'cheese': 'cheddar', 'aardvark': 'Ethel'}
d | e
>>> {'spam': 1, 'eggs': 2, 'cheese': 'cheddar', 'aardvark': 'Ethel'}
e | d
>>> {'cheese': 3, 'aardvark': 'Ethel', 'spam': 1, 'eggs': 2}

# update (|=) operators
d |= e
d
>>> {'spam': 1, 'eggs': 2, 'cheese': 'cheddar', 'aardvark': 'Ethel'}
```
