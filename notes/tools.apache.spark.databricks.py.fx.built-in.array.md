---
id: qprd9xaujm3d7r1sc0w5p9l
title: Array
desc: ''
updated: 1666439965098
created: 1666334219494
---

### Collection Functions

Here are some of the built-in functions available for working with arrays.

| Method         | Description                                                                               |
| -------------- | ----------------------------------------------------------------------------------------- |
| array_contains | Returns null if the array is null, true if the array contains value, and false otherwise. |
| element_at     | Returns element of array at given index. Array elements are numbered starting with **1**. |
| explode        | Creates a new row for each element in the given array or map column.                      |
| collect_set    | Returns a set of objects with duplicate elements eliminated.                              |

```python
%python
mattress_df = (details_df
               .filter(array_contains(col("details"), "Mattress"))
               .withColumn("size", element_at(col("details"), 2)))
display(mattress_df)
```

### Aggregate Functions

Here are some of the built-in aggregate functions available for creating arrays, typically from GroupedData.

| Method       | Description                                                        |
| ------------ | ------------------------------------------------------------------ |
| collect_list | Returns an array consisting of all values within the group.        |
| collect_set  | Returns an array consisting of all unique values within the group. |

<br>

Let's say that we wanted to see the sizes of mattresses ordered by each email address. For this, we can use the **`collect_set`** function

```python
%python
size_df = mattress_df.groupBy("email").agg(collect_set("size").alias("size options"))

display(size_df)
