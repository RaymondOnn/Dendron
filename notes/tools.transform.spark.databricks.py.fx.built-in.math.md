---
id: 22lfxkng69it3q0gnrcwp1z
title: Math
desc: ''
updated: 1666428384184
created: 1666426845021
---
### Math Functions
Here are some of the built-in functions for math operations.

| Method | Description                                                                            |
| ------ | -------------------------------------------------------------------------------------- |
| ceil   | Computes the ceiling of the given column.                                              |
| cos    | Computes the cosine of the given value.                                                |
| log    | Computes the natural logarithm of the given value.                                     |
| round  | Returns the value of the column e rounded to 0 decimal places with HALF_UP round mode. |
| sqrt   | Computes the square root of the specified float value.                                 |

<br>

```python
%python
from pyspark.sql.functions import cos, sqrt

display(spark.range(10)  # Create a DataFrame with a single column called "id" with a range of integer values
        .withColumn("sqrt", sqrt("id"))
        .withColumn("cos", cos("id"))
       )
```
