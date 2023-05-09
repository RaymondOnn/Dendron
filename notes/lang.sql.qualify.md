---
id: g8zt990xk39wsmi5bbo54n0
title: Qualify
desc: ''
updated: 1682985585947
created: 1666745761454
---

Available in: Snowflake, 
Documentation: https://docs.snowflake.com/en/sql-reference/constructs/qualify.html

### How to use?

#### Use Case 1: Deduplicating 
```sql
-- Before qualify
SELECT * 
FROM (
    SELECT 
        *,
        ROW_NUMBER() 
            OVER (
                PARTITION BY cat_id 
                ORDER BY cat_adopted_date DESC) 
        AS rnum
    FROM cats_table
) WHERE rnum = 1;

```

```sql
-- After qualify
SELECT * 
FROM cats_table
QUALIFY 
    ROW_NUMBER() 
        OVER (
            PARTITION BY cat_id 
            ORDER BY cat_adopted_date DESC
        ) = 1
```
Overall, one less query and avoids having to materialize `rnum` column
However, most likely just syntactic sugar, so no performance benefits

#### Use Case 2: Finding Duplicates

```SQL
-- Without Qaulify
WITH cat_dupes AS 
    (SELECT 
        cat_id FROM cat_table
    GROUP BY cat_id
    HAVING COUNT(*) >1)
SELECT * FROM cat_table
WHERE cat_id IN (SELECT cat_id FROM cat_dupes);
```

```sql
-- With Qualify
SELECT * FROM cat_table
QUALIFY COUNT(*) OVER (PARTITION BY cat_id) > 1;
```

``` sql
SELECT *
FROM my_table
QUALIFY COUNT(*) = 0
```