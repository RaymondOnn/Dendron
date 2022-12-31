---
id: dw8rrqadwijiwhsql8ttp5u
title: Any_value
desc: ''
updated: 1670683666378
created: 1670682997188
---

- https://www.alisa-in.tech/post/2019-08-06-snowflake/
- https://roboquery.com/app/syntax-any-value-function-snowflake
- Snowflake Docs: https://docs.snowflake.com/en/sql-reference/functions/any_value.html

### **`ANY_VALUE`**
- Returns some value of the expression from the group. The result is non-deterministic.
- Aggregate function: `ANY_VALUE([DISTINCT] <expr1>)`
- Window function: `ANY_VALUE([DISTINCT] <expr1>) OVER ([PARTITION BY <expr2>])`
- When used as a window function, this function does not support `ORDER BY` sub-clause in the `OVER()` clause.


### Setup:

```SQL
CREATE OR REPLACE TABLE example_any(k int, d decimal(10,5));
INSERT INTO example_any VALUES (1, 1), (1, 5), (1, 10), (2, 2), (2, NULL), (2, 20);

SELECT * FROM example_any;

+---+----------+
| K |        D |
|---+----------|
| 1 |  1.00000 |
| 1 |  5.00000 |
| 1 | 10.00000 |
| 2 |  2.00000 |
| 2 |     NULL |
| 2 | 20.00000 |
+---+----------+
```
<br>

### Return any value from example_any, grouped by k:

```SQL
SELECT k, ANY_VALUE(d) FROM example_any GROUP BY k;

+---+--------------+
| K | ANY_VALUE(D) |
|---+--------------|
| 1 |      1.00000 |
| 2 |      2.00000 |
+---+--------------+
```
<br>

### This example is identical to the previous example, demonstrating the non-determinism of the results:

```SQL
SELECT k, ANY_VALUE(d) FROM example_any GROUP BY k;

+---+--------------+
| K | ANY_VALUE(D) |
|---+--------------|
| 1 |      5.00000 |
| 2 |     20.00000 |
+---+--------------+
```