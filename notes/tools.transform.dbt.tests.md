---
id: eqc1nmd7qys53yxe0rcnjah
title: Tests
desc: ''
updated: 1716194393737
created: 1716194344942
---

### Data contracts

- Contracts define the set of upfront "guarantees" that define the shape of a table.
- By ensuring the upstream tables are as defined by the contract, it ensure that downstream tables will not fail to build.
- Currently only supported for:
  - SQL models.
  - Models materialized as one of the following:
    - `table` 
    - `view`: Views offer limited support for column names and data types, but not constraints.
    - `incremental`: with on_schema_change: append_new_columns or on_schema_change: fail.
    - Certain data platforms, but the supported and enforced constraints vary by platform.

#### Defining Contracts
- Let's say you have a model with a query like:
``` sql
# models/marts/dim_customers.sql
-- lots of SQL

final as (

    select
        customer_id,
        customer_name,
        -- ... many more ...
    from ...

)

select * from final
```
- To enforce a model's contract, set `enforced: true` under the contract configuration.

    ``` yaml
    models/marts/customers.yml
    models:
    - name: dim_customers
        config:
        contract:
            enforced: true   #<--- enabling contract feature
        columns:
        - name: customer_id
            data_type: int
            constraints:
            - type: not_null
        - name: customer_name
            data_type: string
        ...
    ```
    - When enforced, your contract must include every column's `name` and `data_type` (where `data_type` matches one that your data platform understands).
    - If your model is materialized as `table` or `incremental`, and depending on your data platform, you may optionally specify additional constraints, such as `not_null` (containing zero null values).

- When building a model with a defined contract, dbt will do two things differently: