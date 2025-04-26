---
id: 7x5hx4553xe2o16m40zntkm
title: Lineage
desc: ''
updated: 1731170505143
created: 1731170176716
---


### Building DAG from SQL CTEs

- Using this query as example

``` sql
WITH
  cte1 AS (
    SELECT a, b
    FROM table1
  ),
  cte2 AS (
    SELECT c
    FROM table2
  ),
  cte3 AS (
    SELECT a, c
    FROM cte1
    JOIN cte2 ON cte1.b = cte2.c
  ),
  final_result AS (
    SELECT cte1.a, cte3.c
    FROM cte1
    JOIN cte3 ON cte1.a = cte3.a
  )
SELECT *
FROM final_result;
```

``` py

from sqlglot import parse_one, exp

# Step 1: Extract CTEs and their deps
tables = []
for table in parse_one(sql_code).find_all(exp.Table):
    tables.append(table.name)
set(tables)
# output: {'cte1', 'cte2', 'cte3', 'final_result', 'table1', 'table2'}

for cte in parse_one(sql_code).find_all(exp.CTE):
    print(cte.alias_or_name)
# output: cte1, cte2, cte3, final_result

# Step 2: Build the DAG
dependencies = {}
for cte in parse_one(sql_code).find_all(exp.CTE):
    print(cte.alias_or_name)
    dependencies[cte.alias_or_name] = []

    # get subquery in cte "with name as (...)"
    query = cte.this.sql()
    for table in parse_one(query).find_all(exp.Table):
        dependencies[cte.alias_or_name].append(table.name)    
    
print(dependencies)

# Step 3: Visualize the DAG
from graphviz import Digraph

dag = Digraph()

for node in dependencies:
    for dep in dependencies[node]:
        dag.edge(dep, node)

print(dag.source)
dag.render(view=True)
dag
```
