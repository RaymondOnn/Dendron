---
id: hunckzbw6c4b5y6n7yuqb1b
title: destination
desc: ''
updated: 1718595844097
created: 1718595822977
---

### Destination

- Destination is a location in which dlt creates and maintains the current version of the schema and loads your data.
- Destinations come in various forms: databases, datalakes, vector stores or files.
- dlt deals with this variety via modules which you declare when creating a pipeline.

#### Declare the destination type

- Recommended to declare the destination type when creating a pipeline instance with `dlt.pipeline`.
  - Allows the `run` method to synchronize your local pipeline state with destination and extract and normalize to create compatible load packages and schemas.
  - You can also pass the destination to run and load methods.

- Use destination shorthand type

    ``` py
    # Example: Suppose we want to use filesystem built-in destination. 
    # You can use shorthand types only for built-ins.
    
    import dlt

    pipeline = dlt.pipeline("pipeline", destination="filesystem")
    ```

- Use full destination class type

    ``` py
    # Here we use built in filesystem destination by providing a class type filesystem from module dlt.destinations. 
    # You can pass destinations from external modules as well.
    
    import dlt

    pipeline = dlt.pipeline("pipeline", destination="dlt.destinations.filesystem")
    ```

- Import destination class

    ``` py
    # we import destination class for filesystem and pass it to the pipeline.

    import dlt
    from dlt.destinations import filesystem

    pipeline = dlt.pipeline("pipeline", destination=filesystem)
    ```

- All examples above will create the same destination class with default parameters and pull required config and secret values from configuration - they are equivalent.

#### Create new destination class instances

- You can instantiate destination class yourself to configure it explicitly. When doing this you work with destinations the same way you work with sources

    ``` py
    import dlt

    azure_bucket = filesystem("az://dlt-azure-bucket", destination_name="production_az_bucket")
    pipeline = dlt.pipeline("pipeline", destination=azure_bucket)
    ```

- If destination is not named, its shorthand type (the Python class name) serves as a destination name.
- Name your destination explicitly if you need several separate configurations of destinations of the same type (i.e. you wish to maintain credentials for development, staging and production storage buckets in the same config file).
- Destination name is also stored in the load info and pipeline traces so use them also when you need more descriptive names (other than, for example, filesystem).

### Configure a destination

- Recommended to pass the credentials and other required parameters to configuration via TOML files, environment variables or other config providers. Allows for easy switching to production destinations after deployment.

- Recommended to use the default config section layout as below:

    ``` toml
    [destination.filesystem]
    bucket_url="az://dlt-azure-bucket"
    [destination.filesystem.credentials]
    azure_storage_account_name="dltdata"
    azure_storage_account_key="storage key"
    ```

- or via environment variables:

    ``` sh
    DESTINATION__FILESYSTEM__BUCKET_URL=az://dlt-azure-bucket
    DESTINATION__FILESYSTEM__CREDENTIALS__AZURE_STORAGE_ACCOUNT_NAME=dltdata
    DESTINATION__FILESYSTEM__CREDENTIALS__AZURE_STORAGE_ACCOUNT_KEY="storage key"
    ```

- For named destinations you use their names in the config section

    ``` toml
    [destination.production_az_bucket]
    bucket_url="az://dlt-azure-bucket"
    [destination.production_az_bucket.credentials]
    azure_storage_account_name="dltdata"
    azure_storage_account_key="storage key"
    ```

- Note that when you use `dlt init` command to create or add a data source, dlt creates a sample configuration for selected destination.

#### Pass explicit credentials

- You can pass credentials explicitly when creating destination class instance.
- This replaces the credentials argument in `dlt.pipeline` and `pipeline.load` methods - which is now deprecated.
- You can pass the required credentials object, its dictionary representation or the supported native form like below:

    ``` py
    import dlt
    from dlt.destinations import postgres

    # pass full credentials - together with the password (not recommended)
    pipeline = dlt.pipeline(
        "pipeline",
        destination=postgres(credentials="postgresql://loader:loader@localhost:5432/dlt_data"),
    )
    ```

- TIP: You can create and pass partial credentials and dlt will fill the missing data.

    ``` py
    # Example: Passing postgres connection string but without password and expect that it will be present in environment variables (or any other config provider)
    
    import dlt
    from dlt.destinations import postgres

    # pass credentials without password
    # dlt will retrieve the password from ie. DESTINATION__POSTGRES__CREDENTIALS__PASSWORD

    prod_postgres = postgres(credentials="postgresql://loader@localhost:5432/dlt_data")
    pipeline = dlt.pipeline("pipeline", destination=prod_postgres)
    ```

    ``` py
    import dlt
    from dlt.destinations import filesystem
    from dlt.sources.credentials import AzureCredentials

    credentials = AzureCredentials()

    # fill only the account name, leave key to be taken from secrets
    credentials.azure_storage_account_name = "production_storage"
    pipeline = dlt.pipeline(
        "pipeline", destination=filesystem("az://dlt-azure-bucket", credentials=credentials)
    )
    ```

- Please read how to use [various built in credentials types](https://dlthub.com/docs/general-usage/credentials/config_specs).

#### Configure multiple destinations in a pipeline

- To configure multiple destinations within a pipeline, you need to provide the credentials for each destination in the "secrets.toml" file.

    ``` toml
    # This example demonstrates how to configure a BigQuery destination named destination_one:
    [destination.destination_one]
    location = "US"
    [destination.destination_one.credentials]
    project_id = "please set me up!"
    private_key = "please set me up!"
    client_email = "please set me up!"
    ```

- You can then use this destination in your pipeline as follows:

    ``` py
    import dlt
    from dlt.common.destination import Destination

    # Configure the pipeline to use the "destination_one" BigQuery destination

    pipeline = dlt.pipeline(
        pipeline_name='pipeline',
        destination=Destination.from_reference(
            "bigquery",
            destination_name="destination_one"
        ),
        dataset_name='dataset_name'
    )
    ```

- Similarly, you can assign multiple destinations to the same or different drivers.

### Access a destination

- When loading data, dlt will access the destination in two cases:
  - At the beginning of the `run` method to sync the pipeline state with the destination (or if you call `pipeline.sync_destination` explicitly).
  - In the `pipeline.load` method - to migrate schema and load the load package.
- Obviously, dlt will access the destination when you instantiate sql_client.
- NOTE: dlt will not import the destination dependencies or access destination configuration if access is not needed.
  - You can build multi-stage pipelines where steps are executed in separate processes or containers - the extract and normalize step do not need destination dependencies, configuration and actual connection.

    ``` py
    import dlt
    from dlt.destinations import filesystem

    # just declare the destination
    pipeline = dlt.pipeline("pipeline", destination="filesystem")

    # no destination credentials not config needed to extract
    pipeline.extract(["a", "b", "c"], table_name="letters")

    # same to normalize
    pipeline.normalize()

    # here dependencies dependencies will be imported, secrets pulled and destination accessed
    # we pass bucket_url explicitly and expect credentials passed by config provider

    load_info = pipeline.load(destination=filesystem(bucket_url=bucket_url))
    load_info.raise_on_failed_jobs()
    ```

#### Create new destination

- You have two ways to implement a new destination:
  - You can use `@dlt.destination` decorator and implement a sink function. This is perfect way to implement reverse ETL destinations that push data back to REST APIs.
  - You can implement a full destination where you have a full control over load jobs and schema migration.

## Destination tables

- When you run a pipeline, dlt creates tables in the destination database and loads the data from your source into these tables.

    ``` py
    # We start with a simple dlt pipeline:

    import dlt

    data = [
        {'id': 1, 'name': 'Alice'},
        {'id': 2, 'name': 'Bob'}
    ]

    # Here we are using the DuckDb destination, which is an in-memory database. 
    # Other database destinations will behave similarly and have similar concepts.
    pipeline = dlt.pipeline(
        pipeline_name='quick_start',
        destination='duckdb',
        dataset_name='mydata'
    )
    
    # Running this pipeline will create a database schema in the destination 
    # database (DuckDB) along with a table named users. 
    load_info = pipeline.run(data, table_name="users")
    ```

>Quick tip: you can use the show command of the dlt pipeline CLI to see the tables in the destination database.

#### Database schema

- The database schema is a collection of tables that represent the data you loaded into the database.
- The schema name is the same as the `dataset_name` you provided in the pipeline definition.
  - In the example above, we explicitly set the `dataset_name` to mydata.
  - If you don't set it, it will be set to the pipeline name with a suffix _dataset.

- Be aware that the schema referred to in this section is distinct from the dlt Schema.
  - The database schema pertains to the structure and organization of data within the database, including table definitions and relationships.
  - On the other hand, the "dlt Schema" specifically refers to the format and structure of normalized data within the dlt pipeline.

#### Tables

- Each resource in your pipeline definition will be represented by a table in the destination.
- In the example above, we have one resource, users, so we will have one table, mydata.users, in the destination.
  - Where mydata is the schema name, and users is the table name.
  - Here also, we explicitly set the table_name to users. When table_name is not set, the table name will be set to the resource name.

``` py
# For example, we can rewrite the pipeline above as:

@dlt.resource
def users():
    yield [
        {'id': 1, 'name': 'Alice'},
        {'id': 2, 'name': 'Bob'}
    ]

pipeline = dlt.pipeline(
    pipeline_name='quick_start',
    destination='duckdb',
    dataset_name='mydata'
)
load_info = pipeline.run(users)

# The result will be the same; 
# the table is implicitly named users based on the resource name 
# (e.g., users() decorated with @dlt.resource).
```

- Special tables are created to track the pipeline state.
  - These tables are prefixed with _dlt_ and are not shown in the show command of the dlt pipeline CLI. However, you can see them when connecting to the database directly.

#### Child and parent tables

- Now let's look at a more complex example:
  - Running this pipeline will create two tables in the destination, users and users__pets.
  - The users table will contain the top-level data
  - the users__pets table will contain the child data.

    ``` py
    import dlt

    data = [
        {
            'id': 1,
            'name': 'Alice',
            'pets': [
                {'id': 1, 'name': 'Fluffy', 'type': 'cat'},
                {'id': 2, 'name': 'Spot', 'type': 'dog'}
            ]
        },
        {
            'id': 2,
            'name': 'Bob',
            'pets': [
                {'id': 3, 'name': 'Fido', 'type': 'dog'}
            ]
        }
    ]

    pipeline = dlt.pipeline(
        pipeline_name='quick_start',
        destination='duckdb',
        dataset_name='mydata'
    )
    load_info = pipeline.run(data, table_name="users")
    ```

  - Here is what the tables may look like:
    - `mydata.users`

        |id |name  | _dlt_id        | _dlt_load_id     |
        |---|------|----------------|------------------|
        |1  |Alice | wX3f5vn801W16A | 1234562350.98417 |
        |2  |Bob   | rX8ybgTeEmAmmA | 1234562350.98417 |

    - `mydata.users__pets`

        | id  | name   | type | _dlt_id        | _dlt_parent_id | _dlt_list_idx |
        | --- | ------ | ---- | -------------- | -------------- | ------------- |
        | 1   | Fluffy | cat  | w1n0PEDzuP3grw | wX3f5vn801W16A | 0             |
        | 2   | Spot   | dog  | 9uxh36VU9lqKpw | wX3f5vn801W16A | 1             |
        | 3   | Fido   | dog  | pe3FVtCWz8VuNA | rX8ybgTeEmAmmA | 0             |

- When creating a database schema, dlt recursively unpacks nested structures into relational tables, creating and linking children and parent tables.

##### This is how it works

1. Each row in all (top level and child) data tables created by dlt contains a `UNIQUE` column named `_dlt_id`.
1. Each child table contains a `FOREIGN KEY` column `_dlt_parent_id` linking to a particular row (`_dlt_id`) of a parent table.
1. Rows in child tables come from the lists: dlt stores the position of each item in the list in `_dlt_list_idx`.
1. For tables that are loaded with the merge write disposition, we add a root key column `_dlt_root_id`, which links the child table to a row in the top-level table.

- If you define your own primary key in a child table, it will be used to link to the parent table, and the `_dlt_parent_id` and `_dlt_list_idx` will not be added.
- `_dlt_id` is always added even if the primary key or other unique columns are defined.

### Naming convention: tables and columns

- During a pipeline run, dlt normalizes both table and column names to ensure compatibility with the destination database's accepted format.
- All **names from your source data will be transformed into snake_case** and will only include alphanumeric characters. The names in the destination database may differ somewhat from those in your original input.

#### Variant columns

- If your data has inconsistent types, dlt will dispatch the data to several variant columns.
- The general naming rule for variant columns is `<original name>__v_<type>` where original_name is the existing column name (with data type clash) and type is the name of the data type stored in the variant.
  - For e.g. if you have a resource (i.e., JSON file) with a field with name answer and your data contains boolean values, you will get a column with name `answer` of type `BOOLEAN` in your destination.
  - If for some reason, on the next load, you get integer and string values in answer, the inconsistent data will go to `answer__v_bigint` and `answer__v_text` columns respectively.

### Load Packages and Load IDs

- Each execution of the pipeline generates one or more load packages.
  - A load package typically contains data retrieved from all the resources of a particular source.
  - These packages are uniquely identified by a load_id.
  - The load_id of a particular package is added to the top data tables (referenced as `_dlt_load_id` column in the example above) and to the special_dlt_loads table with a status of 0 (when the load process is fully completed).

- To illustrate this, let's load more data into the same destination:

  ``` json
  data = [
      {
          'id': 3,
          'name': 'Charlie',
          'pets': []
      },
  ]
  ```

- The rest of the pipeline definition remains the same.
- Running this pipeline will create a new load package with a new load_id and add the data to the existing tables.
  - The users table `mydata.users` will now look like this:

    |id |name    | _dlt_id        | _dlt_load_id     |
    |---|--------|----------------|------------------|
    |1  |Alice   | wX3f5vn801W16A | 1234562350.98417 |
    |2  |Bob     | rX8ybgTeEmAmmA | 1234562350.98417 |
    |3  |Charlie | h8lehZEvT3fASQ | 1234563456.12345 |

  - The `_dlt_loads` table `mydata._dlt_loads` will look like this:

    | load_id          | schema_name | status | inserted_at                  | schema_version_hash |
    | ---------------- | ----------- | ------ | ---------------------------- | ------------------- |
    | 1234562350.98417 | quick_start | 0      | 2023-09-12 16:45:51.17865+00 | aOEb...Qekd/58=     |
    | 1234563456.12345 | quick_start | 0      | 2023-09-12 16:46:03.10662+00 | aOEb...Qekd/58=     |

    - The `_dlt_loads` table tracks complete loads and allows chaining transformations on top of them.
    - Many destinations do not support distributed and long-running transactions (e.g., Amazon Redshift).
    - In that case, the user may see the partially loaded data.
    - It is possible to filter such data out: any row with a load_id that does not exist in_dlt_loads is not yet completed.
    - The same procedure may be used to identify and delete data for packages that never got completed.

- For each load, you can test and alert on anomalies (e.g., no data, too much loaded to a table). 
- There are also some useful load stats in the Load info tab of the Streamlit app mentioned above.
- You can add transformations and chain them together using the status column. 
- You start the transformation for all the data with a particular load_id with a status of 0 and then update it to 1. 
- The next transformation starts with the status of 1 and is then updated to 2. This can be repeated for every additional transformation.

#### Data lineage

- Data lineage can be super relevant for architectures like the data vault architecture or when troubleshooting. 
- The data vault architecture is a data warehouse that large organizations use when representing the same process across multiple systems, which adds data lineage requirements.
- Using the pipeline name and `load_id` provided out of the box by dlt, you are able to identify the source and time of data.
- You can save complete lineage info for a particular `load_id` including a list of loaded files, error messages (if any), elapsed times, schema changes. This can be helpful, for example, when troubleshooting problems.

#### Staging dataset

So far we've been using the append write disposition in our example pipeline. This means that each time we run the pipeline, the data is appended to the existing tables. When you use the merge write disposition, dlt creates a staging database schema for staging data. This schema is named <dataset_name>_staging and contains the same tables as the destination schema. When you run the pipeline, the data from the staging tables is loaded into the destination tables in a single atomic transaction.

Let's illustrate this with an example. We change our pipeline to use the merge write disposition:

import dlt

@dlt.resource(primary_key="id", write_disposition="merge")
def users():
    yield [
        {'id': 1, 'name': 'Alice 2'},
        {'id': 2, 'name': 'Bob 2'}
    ]

pipeline = dlt.pipeline(
    pipeline_name='quick_start',
    destination='duckdb',
    dataset_name='mydata'
)

load_info = pipeline.run(users)

Running this pipeline will create a schema in the destination database with the name mydata_staging. If you inspect the tables in this schema, you will find the mydata_staging.users table identical to themydata.users table in the previous example.

Here is what the tables may look like after running the pipeline:

mydata_staging.users

id name _dlt_id _dlt_load_id
1 Alice 2 wX3f5vn801W16A 2345672350.98417
2 Bob 2 rX8ybgTeEmAmmA 2345672350.98417
mydata.users

id name _dlt_id _dlt_load_id
1 Alice 2 wX3f5vn801W16A 2345672350.98417
2 Bob 2 rX8ybgTeEmAmmA 2345672350.98417
3 Charlie h8lehZEvT3fASQ 1234563456.12345
Notice that the mydata.users table now contains the data from both the previous pipeline run and the current one.

#### Versioned datasets

When you set the full_refresh argument to True in dlt.pipeline call, dlt creates a versioned dataset. This means that each time you run the pipeline, the data is loaded into a new dataset (a new database schema). The dataset name is the same as the dataset_name you provided in the pipeline definition with a datetime-based suffix.

We modify our pipeline to use the full_refresh option to see how this works:

import dlt

data = [
    {'id': 1, 'name': 'Alice'},
    {'id': 2, 'name': 'Bob'}
]

pipeline = dlt.pipeline(
    pipeline_name='quick_start',
    destination='duckdb',
    dataset_name='mydata',
    full_refresh=True # <-- add this line
)
load_info = pipeline.run(data, table_name="users")

Every time you run this pipeline, a new schema will be created in the destination database with a datetime-based suffix. The data will be loaded into tables in this schema. For example, the first time you run the pipeline, the schema will be named mydata_20230912064403, the second time it will be named mydata_20230912064407, and so on.
