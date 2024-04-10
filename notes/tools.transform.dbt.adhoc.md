---
id: 3wdhcvuhn1byf6fe5dsfeh5
title: adhoc
desc: ''
updated: 1709279064846
created: 1709185976973
---

### Analyses: Adhoc queries

- Analyses are .sql files that live in the `analyses` folder.
- Analyses will not be run with `dbt run` like models.
- Can still compile these from Jinja-SQL to pure SQL using `dbt compile`. These will compile to the target folder.
- Analyses are useful for training queries, one-off queries, and audits

### Seeds: Adhoc Sources
- Seeds are `.csv` files that live in the `seeds` folder 
  - `> dbt 1.0.0`: seeds live in the `data` folder
- Seeds can be references using the `ref` macro
- For static data that doesn't change frequently and **NOT** for uploading data that changes frequently
  - i.e. loading country codes, employee emails, or employee account IDs

- `dbt seed`: seeds will be built in the DWH as tables. 

#### Testing Seeds
``` yaml

version: 2

seeds:
    - name: employees
        description: a manual map of employees to customers
        columns:
            - name: employee_id
                tests:
                    - unique
                    - not_null
```

- `dbt test --models employees`: Run test for `employee` seed


#### Seeds

DBT seed is a way to store CSV files or mapping files as a code and load them directly into the database. I have already written a detailed article on this topic you can check here.