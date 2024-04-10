---
id: s9ao6bo07n7t99fygerrpyp
title: functions
desc: ''
updated: 1707603466265
created: 1707602959433
---

### `@validate_call`
- In Pydantic V2, you can validate the arguments of functions easily, making sure they have the right types and values. This can make your code cleaner and more organized.
``` py
from pydantic import validate_call, conint


@validate_call
def greet_times(times: conint(gt=0, lt=11), name: str):
    for _ in range(times):
        print(f"Hello - {name}")


# Valid.
print("-------Valid--------")
greet_times(times=3, name="John Doe")

# Invalid, times must be from 1 to 10.
try:
    print("-------Invalid--------")
    greet_times(times=12, name="D. B. Cooper")
except Exception as e:
    print(e)

# Output:

-------Valid--------
Hello - John Doe
Hello - John Doe
Hello - John Doe
-------Invalid--------
1 validation error for greet_times
times
  Input should be less than 11 [type=less_than, input_value=12, input_type=int]
    For further information visit https://errors.pydantic.dev/2.0.1/v/less_than
```