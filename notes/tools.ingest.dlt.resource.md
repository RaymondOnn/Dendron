---
id: 3k19lcbgbbc7h1o91vwwqp1
title: resource
desc: ''
updated: 1718578166458
created: 1718553360941
---

- [Resource](#resource)
  - [Defining schema](#defining-schema)
    - [Validating schemas with data contracts](#validating-schemas-with-data-contracts)
    - [Define schema with Pydantic](#define-schema-with-pydantic)
  - [Dynamically splitting a resource into multiple tables](#dynamically-splitting-a-resource-into-multiple-tables)
  - [Parametrize a resource](#parametrize-a-resource)
  - [Process resources with `dlt.transformer`](#process-resources-with-dlttransformer)
  - [Declare a standalone resource](#declare-a-standalone-resource)
  - [Declare parallel and async resources](#declare-parallel-and-async-resources)
- [Customize resources](#customize-resources)
  - [Reduce the nesting level of generated tables](#reduce-the-nesting-level-of-generated-tables)
  - [Sample from large data](#sample-from-large-data)
  - [Set table name and adjust schema](#set-table-name-and-adjust-schema)
  - [Adjust schema when you yield data](#adjust-schema-when-you-yield-data)
  - [Duplicate and rename resources](#duplicate-and-rename-resources)
- [Load resources](#load-resources)
  - [Do a full refresh](#do-a-full-refresh)

### Resource

- A resource is an (optionally async) function that yields data.
- To create a resource, we add the `@dlt.resource` decorator to that function.
- Commonly used arguments:
  - `name`: The name of the table generated by this resource. Defaults to decorated function name.
  - `write_disposition` define how the data be loaded at destination
    - Currently supported: `append`, `replace` and `merge`. Defaults to append.
- Typically, `resources` are declared and grouped with related `resources` within a `source` function.

    ``` py
    @dlt.resource(name='table_name', write_disposition='replace')
    def generate_rows():
        for i in range(10):
            yield {'id':i, 'example_string':'abc'}

    @dlt.source
    def source_name():
        return generate_rows
    ```

- To get the data of a resource, we could do:

    ``` py
    for row in generate_rows():
        print(row)

    for row in source_name().resources.get('table_name'):
        print(row)
    ```

#### Defining schema

- dlt will infer schema for tables associated with resources from the resource's data.
- You can modify the generation process by using the table and column hints.
  - Resource decorator accepts following arguments:
    - `table_name`: the name of the table, if different from resource name.
    - `primary_key` and `merge_key`: define name of the columns (compound keys are allowed) that will receive those hints. Used in incremental loading.
    - `columns`: define one or more columns, including the data types, nullability and other hints.
      - The column definition is a `TypedDict: TTableSchemaColumns`.

    ``` py
    # Column tags (containing a list of tags) in user table have type complex so that 
    # it will be loaded as JSON/struct and not as child table.
    @dlt.resource(name="user", columns={"tags": {"data_type": "complex"}})
    def get_users():
    ...

    # the `table_schema` method gets table schema generated by a resource
    print(get_users().compute_table_schema())
    ```

##### Validating schemas with data contracts

- Use the `schema_contract` argument to tell dlt how to deal with new tables, data types and bad data types.
- For e.g. `schema_contract=freeze` will not allow for any new tables, columns or data types to be introduced to the schema but instead raise an exception. Learn more in on available contract modes [here](https://dlthub.com/docs/general-usage/schema-contracts#setting-up-the-contract)

##### Define schema with Pydantic

- You can alternatively use a Pydantic model to define the schema.

    ``` py
    from pydantic import BaseModel


    class Address(BaseModel):
        street: str
        city: str
        postal_code: str


    class User(BaseModel):
        id: int
        name: str
        tags: List[str]
        email: Optional[str]
        address: Address
        status: Union[int, str]


    @dlt.resource(name="user", columns=User)
    def get_users():
        ...
    ```

- The data types of the table columns are inferred from the types of the pydantic fields.
  - Fields with an Optional type are marked as nullable
  - Fields with a Union type are converted to the first (not None) type listed in the union. E.g. status: Union[int, str] results in a bigint column.
  - list, dict and nested pydantic model fields will use the complex type which means they'll be stored as a JSON object in the database instead of creating child tables.
  - You can override this by configuring the Pydantic model

        ``` py
        from typing import ClassVar
        from dlt.common.libs.pydantic import DltConfig

        # "skip_complex_types" omits any dict/list/BaseModel type fields from the schema
        # dlt will fall back on the default behaviour of creating child tables for these fields.
        class UserWithNesting(User):
        dlt_config: ClassVar[DltConfig] = {"skip_complex_types": True}

        @dlt.resource(name="user", columns=UserWithNesting)
        def get_users():
            ...
        ```

#### Dynamically splitting a resource into multiple tables

- You can load data to many tables from a single resource.
- The most common case is a **stream of events of different types**, each with different data schema.

    ``` py
    # Example: a resource that loads GitHub repository events wants to send issue, pull request, and comment events to separate tables. The type of the event is in the "type" field.
    
    # send item to a table with name item["type"]
    @dlt.resource(table_name=lambda event: event['type'])
    def repo_events() -> Iterator[TDataItems]:
        yield item

    # the `table_schema` method gets table schema generated by a resource and takes optional
    # data item to evaluate dynamic hints
    print(repo_events().compute_table_schema({"type": "WatchEvent", id:...}))
    ```

- In more advanced cases, you can dispatch data to different tables directly in the code of the resource function:

    ``` py
    @dlt.resource
    def repo_events() -> Iterator[TDataItems]:
        # mark the "item" to be sent to table with name item["type"]
        yield dlt.mark.with_table_name(item, item["type"])
    ```

#### Parametrize a resource

- You can add arguments to your resource functions like to any other. Below we parametrize our generate_rows resource to generate the number of rows we request:

    ``` py
    @dlt.resource(name='table_name', write_disposition='replace')
    def generate_rows(nr):
        for i in range(nr):
            yield {'id':i, 'example_string':'abc'}

    for row in generate_rows(10):
        print(row)

    for row in generate_rows(20):
        print(row)
    ```

- You can mark some resource arguments as configuration and credentials values so dlt can pass them automatically to your functions.

#### Process resources with `dlt.transformer`

- You can **feed data from a resource into another one** for e.g.when you have an API that returns a list of objects (i.e. users) in one endpoint and user details in another.

    ``` py
    # Here, user_details will receive data from default instance of users resource (with limit set to None). 
    # You can also use pipe | operator to bind resources dynamically

    @dlt.resource(write_disposition="replace")
    def users(limit=None):
        for u in _get_users(limit):
            yield u

    # feed data from users as user_item below,
    # all transformers must have at least one 
    # argument that will receive data from the parent resource
    @dlt.transformer(data_from=users)
    def users_details(user_item):
        for detail in _get_details(user_item["user_id"]):
            yield detail

    # just load the user_details.
    # dlt figures out dependencies for you.
    pipeline.run(user_details)

    # you can be more explicit and use a pipe operator
    # with it you can create dynamic pipelines where he dependencies
    # are set at run time and resources are parametrized i.e
    # below we want to load only 100 users from `users` endpoint
    pipeline.run(users(limit=100) | user_details)
    ```

- Transformers can yield / return values and can decorate async functions and async generators.

    ``` py
    # Example: Decorate an async function and request details on two pokemons. 
    # Http calls are made in parallel via httpx library.
    
    import dlt
    import httpx

    @dlt.transformer
    async def pokemon(id):
        async with httpx.AsyncClient() as client:
            r = await client.get(f"<https://pokeapi.co/api/v2/pokemon/{id}>")
            return r.json()

    # get bulbasaur and ivysaur (you need dlt 0.4.6 for pipe operator working with lists)

    print(list([1,2] | pokemon()))
    ```

#### Declare a standalone resource

- A standalone resource is defined on a function that is top level in a module (not inner function) that **accepts config and secrets values**.
  - if standalone flag is specified, the decorated function signature and docstring will be preserved.
  - `dlt.resource` will just wrap the decorated function and user must call the wrapper to get the actual resource.

    ``` py
    # Example: declaring a filesystem resource that must be called before use.
    
    @dlt.resource(standalone=True)
    def filesystem(bucket_url=dlt.config.value):
    """list and yield files in `bucket_url`"""
    ...

    # `filesystem` must be called before it is extracted or used in any other way
    pipeline.run(filesystem("s3://my-bucket/reports"), table_name="reports")
    ```

- Standalone may have dynamic name that depends on the arguments passed to the decorated function.

    ``` py
    @dlt.resource(standalone=True, name=lambda args: args["stream_name"])
    def kinesis(stream_name: str):
        ...

    kinesis_stream = kinesis("telemetry_stream")

    kinesis_stream resource has a name telemetry_stream
    ```

#### Declare parallel and async resources

- You can extract multiple resources in parallel threads or with async IO.
- To enable this for a sync resource you can set `parallelized=True` in the resource decorator:

    ``` py
    @dlt.resource(parallelized=True)
    def get_users():
        for u in_get_users():
            yield u

    @dlt.resource(parallelized=True)
    def get_orders():
        for o in_get_orders():
            yield o

    # users and orders will be iterated in parallel in two separate threads

    pipeline.run([get_users(), get_orders()])
    ```

  - Async generators are automatically extracted concurrently with other resources:

  ``` py
  @dlt.resource
  async def get_users():
      async for u in_get_users():  # Assuming_get_users is an async generator
          yield u
  ```

- Please find more details in [extract performance](https://dlthub.com/docs/reference/performance#extract)

### Customize resources

Filter, transform and pivot data

- You can attach any number of transformations that are evaluated on item per item basis to your resource.
- The available transformation types:
  - map: apply transformation to every data item (`resource.add_map`).
  - filter: filter the data item (`resource.add_filter`).
  - yield map: a map that returns iterator (so single row may generate many rows - `resource.add_yield_map`).

    ``` py
    # Example: We have a resource that loads a list of users from an api endpoint. 
    # We want to customize it by removing users with user_id == "me", and then anonymize user data.

    import dlt

    # Here's our resource:
    @dlt.resource(write_disposition="replace")
    def users():
        ...
        users = requests.get(...)
        ...
        yield users

    # Here's our script that defines transformations and loads the data:
    from pipedrive import users

    def anonymize_user(user_data):
        user_data["user_id"] = hash_str(user_data["user_id"])
        user_data["user_email"] = hash_str(user_data["user_email"])
        return user_data

    # add the filter and anonymize function to users resource and enumerate
    for user in users().add_filter(lambda user: user["user_id"] != "me").add_map(anonymize_user):
        print(user)
    ```

#### Reduce the nesting level of generated tables

- You can limit how deep dlt goes when generating child tables.
- By default, the library will descend and generate child tables for all nested lists, without limit.
- NOTE:
  - `max_table_nesting` is optional so you can skip it,
  - in this case dlt will use it from the source if it is specified there or fallback to default value which has 1000 as maximum nesting level.

``` py
Example: Suppose we want only 1 level of child tables to be generated (so there are no child tables of child tables). 
import dlt

@dlt.resource(max_table_nesting=1)
def my_resource():
    yield {
        "id": 1,
        "name": "random name",
        "properties": [
            {
                "name": "customer_age",
                "type": "int",
                "label": "Age",
                "notes": [
                    {
                        "text": "string",
                        "author": "string",
                    }
                ]
            }
        ]
    }
```

- Typical settings:
  - `max_table_nesting=0`: No child tables. All nested data will be represented as json.
  - `max_table_nesting=1` Child tables of top level tables only. All nested data in child tables will be represented as json.
  - You can achieve the same effect after the resource instance is created:

    ``` py
    from my_resource import my_awesome_module

    resource = my_resource()
    resource.max_table_nesting = 0
    ```

> TIP:
>
> - Several data sources are prone to contain semi-structured documents with very deep nesting i.e. MongoDB databases.
> - Our practical experience is that setting the `max_nesting_level` to 2 or 3 produces the clearest and human-readable schemas.

#### Sample from large data

- Great for getting a sample from a large dataset to quickly see the dataset with example data and test your transformations etc.
- `resource.add_limit`: adds a limit on the number of items yielded by a resource.
  - Closes the iterator/generator that produces data after limit is reached.
  - You cannot limit transformers. They should process all the data they receive fully to avoid inconsistencies in generated datasets.
  - Set limit to None or -1 to disable the limiting.
  - Set limit to 0 for the resource to not yield any items.
  
    ``` py
    r = dlt.resource(itertools.count(), name="infinity").add_limit(10)
    assert list(r) == list(range(10))
    ```

#### Set table name and adjust schema

- You can change the schema of a resource, be it standalone or as a part of a source.
- Look for method named `apply_hints` which takes the same arguments as resource decorator.
- Obviously you should call this method before data is extracted from the resource.

``` py
# Example: Changing resource loading the users table from 'append' to 'merge', keeping one updated record per user_id. 
# It also adds "last value" incremental loading on created_at column to prevent requesting again the already loaded records:

tables = sql_database()
tables.users.apply_hints(
    write_disposition="merge",
    primary_key="user_id",
    incremental=dlt.sources.incremental("updated_at")
)
pipeline.run(tables)

# To just change a name of a table to which resource will load data, do the following:

tables = sql_database()
tables.users.table_name = "other_users"
```

#### Adjust schema when you yield data

- You can set or update the table name, columns and other schema elements when your resource is executed and you already yield data.
- Such changes will be merged with the existing schema in the same way apply_hints method above works.
- There are many reason to adjust schema at runtime.
  - For e.g. when using Airflow, you should avoid lengthy operations (ie. reflecting database tables) during creation of the DAG so it is better do do it when DAG executes.
  - You may also emit partial hints (ie. precision and scale for decimal types) for column to help dlt type inference.

    ``` py
    @dlt.resource
    def sql_table(credentials, schema, table):
        # create sql alchemy engine
        engine = engine_from_credentials(credentials)
        engine.execution_options(stream_results=True)
        metadata = MetaData(schema=schema)
        # reflect the table schema
        table_obj = Table(table, metadata, autoload_with=engine)

        for idx, batch in enumerate(table_rows(engine, table_obj)):
        if idx == 0:
            # emit first row with hints, table_to_columns and get_primary_key are helpers that extract dlt schema from
            # SqlAlchemy model
            yield dlt.mark.with_hints(
                batch,
                dlt.mark.make_hints(columns=table_to_columns(table_obj), primary_key=get_primary_key(table_obj)),
            )
        else:
            # just yield all the other rows
            yield batch
    ```

    - In the example above we use `dlt.mark.with_hints` and `dlt.mark.make_hints` to emit columns and primary key with the first extracted item.
    - Table schema will be adjusted after the batch is processed in the extract pipeline but before any schema contracts are applied and data is persisted in load package.

> TIP
>
> - You can emit columns as Pydantic model and use dynamic hints (ie. lambda for table name) as well.
> - You should avoid redefining Incremental this way.

#### Duplicate and rename resources

- There are cases when you your resources are generic (ie. bucket filesystem) and you want to load several instances of it (ie. files from different folders) to separate tables.
- `with_name`: returns a deep copy of the original resource, its data pipe and the data pipes of a parent resources.
- A renamed clone is fully separated from the original resource (and other clones) when loading: it maintains a separate resource state and will load to a table

    ``` py
    # Example: Using filesystem source to load csvs from two different folders into separate tables:

    @dlt.resource(standalone=True)
    def filesystem(bucket_url):

    # list and yield files in bucket_url

    ...

    @dlt.transformer
    def csv_reader(file_item):

    # load csv, parse and yield rows in file_item

    ...

    # create two extract pipes that list files from the bucket and send to them to the reader
    # by default both pipes will load data to the same table (csv_reader)

    reports_pipe = filesystem("s3://my-bucket/reports") | load_csv()
    transactions_pipe = filesystem("s3://my-bucket/transactions") | load_csv()

    # so we rename resources to load to "reports" and "transactions" tables
    pipeline.run(
    [reports_pipe.with_name("reports"), transactions_pipe.with_name("transactions")]
    )
    ```

### Load resources

- You can pass individual resources or list of resources to the `dlt.pipeline` object.
- The resources loaded outside the source context, will be added to the default schema of the pipeline.

    ``` py
    @dlt.resource(name='table_name', write_disposition='replace')
    def generate_rows(nr):
        for i in range(nr):
            yield {'id':i, 'example_string':'abc'}

    pipeline = dlt.pipeline(
        pipeline_name="rows_pipeline",
        destination="duckdb",
        dataset_name="rows_data"
    )

    # load individual resource
    pipeline.run(generate_rows(10))

    # load a list of resources
    pipeline.run([generate_rows(10), generate_rows(20)])
    ```

#### Do a full refresh

- To do a full refresh of an append or merge resources you temporarily change the write disposition to replace.
- You can use `apply_hints` method of a resource or just provide alternative write disposition when loading:

    ```
    p.run(merge_source(), write_disposition="replace")
    ```