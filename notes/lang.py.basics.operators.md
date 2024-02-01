---
id: 0nmctix14u2q6e0niabgb8d
title: Operators
desc: ''
updated: 1705716902770
created: 1705715659802
---

### Walrus Operator (`:=`)
- from python 3.8 onwards
``` py
def get_data():
    for i in range(10):
        yield i
    yield -1

def process(data):
    print(data)

# before:
gen = get_data():
data = next(gen)
while data != -1:
    process(data)
    data = next(gen)

# after:
gen = get_data():

while (data := next(gen)) != -1:
    process(data)
```
``` py
def f(x):
    return x-1
    
# before: f(x) is computed twice
results = [f(x) for x in range(10) if f(x) > 3]

# after: f(x) is computed once
results = [result for x in range(10) if (result := f(x)) > 3]
```
``` py
# "func" called 3 times
result = [func(x), func(x)**2, func(x)**3]

# Reuse result of "func" without splitting the code into multiple lines
result = [y := func(x), y**2, y**3]
```

### Dictionary Unions
- Another interesting addition to Python operators. Both are used for dictionary unions. We have the merge operator |:
``` py
a = {1: 'a', 2: 'b', 3: 'c'}
b = {4: 'd', 5: 'e'}
c = a | b
print(c)
[Out]: {1: 'a', 2: 'b', 3: 'c', 4: 'd', 5: 'e'}
```

And the update operator |=, which allows us to do the merge in-place:
``` py
a = {1: 'a', 2: 'b', 3: 'c'}
b = {4: 'd', 5: 'e'}a |= b
print(a)
[Out]: {1: 'a', 2: 'b', 3: 'c', 4: 'd', 5: 'e'}
```

