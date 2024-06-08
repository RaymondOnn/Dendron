---
id: uzt8ss4vvpfdeygov80wjkb
title: sources
desc: ''
updated: 1716194385976
created: 1716190111911
---

### Sources
- Sources make it possible to name and describe the data loaded into your warehouse by your Extract and Load tools. 
- By declaring these tables as sources in dbt, you can then
  - select from source tables in your models using the `{{ source() }}` function, helping define the lineage of your data
  - test your assumptions about your source data
  - calculate the freshness of your source data

#### Declaring sources
- Sources are defined in `.yml` files nested under `sources:` key.
- By default, schema will be the same as name. Add schema only if you want to use a source name that differs from the existing schema.
- `sources.yml` is also where you 
  - Add data tests for sources
  - Add descriptions to sources, that get rendered as part of your documentation site

    ```yaml
    # models/staging/src_jaffle_shop.yml
    version: 2

    sources:
        - name: jaffle_shop # source name
        database: raw
        schema: jaffle_shop
        freshness: # default freshness
            warn_after: {count: 12, period: hour}
            error_after: {count: 24, period: hour}
        loaded_at_field: _etl_loaded_at
        
        tables:
            - name: customers # raw.jaffle_shop.customers
            - name: orders # raw.jaffle_shop.orders
                loaded_at_field: _etl_loaded_at # reference timestamp field. Field must be in the table
                freshness: null # dont check freshness 
    ```

#### Referencing defined sources
- Once a source has been defined, it can be referenced from a model using the `{{ source()}}` function. dbt will compile it to the full table name.
- Using the `{{ source () }}` function also creates a dependency between the model and the source table.

    ```sql
    -- references to the YAML file created
    ...
    FROM {{ source ('jaffle_shop', 'customers') }}
    ...
    ```

### Testing sources
-   `dbt source freshness`: check the freshness of source data
    -   Behind the scenes, dbt uses the freshness properties to construct and execute a select query.
        ``` sql
        select
            max(_etl_loaded_at) as max_loaded_at,
            convert_timezone('UTC', current_timestamp()) as snapshotted_at
        from raw.jaffle_shop.orders
        ```
    - To prevent a full table scan, we can add a `filter` argument to the config for e.g. `filter: _etl_loaded_at >= date_sub(current_date(), interval 1 day).` The resulting query would look like:
        ``` sql
        select
        max(_etl_loaded_at) as max_loaded_at,
        convert_timezone('UTC', current_timestamp()) as snapshotted_at
        from raw.jaffle_shop.orders
        where _etl_loaded_at >= date_sub(current_date(), interval 1 day)
        ```

  - dbt will run a "preflight" check to ensure that the model's query will return a set of columns with names and data types matching the ones you have defined. This check is agnostic to the order of columns specified in your model (SQL) or YAML spec.
dbt will include the column names, data types, and constraints in the DDL statements it submits to the data platform, which will be enforced while building or updating the model's table.