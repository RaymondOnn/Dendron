---
id: ksdm3p712exbx5g8xqfu36i
title: Erd
desc: ''
updated: 1733566198570
created: 1731170759485
---
<https://medium.com/datascale/creating-er-diagram-from-sql-query-224f796afdb9>

### What’s an ERD?

A diagram that depicts the relationships between entities. In this case, it represents an overall logical structure of a database.

#### Extract tables, columns and relationships

``` py
# Get the tables
from sqlglot import parse_one, exp

for table in parse_one(sql_code, read='mysql').find_all(exp.Table)
    print(f"table: {table.name}, {table.alias_or_name}")

# Getting the columns
from sql_metadata import Parser

parsed = Parser(sql_code)
print(parsed.tables, parsed.columns)

# Mapping relationship between columns
for join_stmt in sql_expression.find_all(exp.Join):
    print(join_stmt)
    for from_stmt in join_stmt.parent_select.find_all(exp.From):
        print(from_stmt)
        from table in from_stmt.find_all(exp.Table)
            print(f"TABLE: {table.name}, {table..alias_or_name}")
    print()
```

#### DDL Parser

``` py
from simple_ddl_parser import DDLParser

ddl = """
CREATE TABLE follows(
 follower_id INT NOT NULL,
 followee_id INT NOT NULL,
 created_at TIMESTAMP DEFAULT NOW(),
 FOREIGN KEY (follower_id) REFERENCES users(id),
 FOREIGN KEY (followee_id) REFERENCES users(id),
 PRIMARY KEY(follower_id,followee_id)
);
"""

results = DDLParser(ddl).run(output_mode="sql")
results


```
