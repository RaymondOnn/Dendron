---
id: t8qky7dxcwb8g666tz0yovp
title: Glue
desc: ''
updated: 1691123890236
created: 1690990131570
---



## `awsglue`
### Standard Glue Snippet

```py
try:
    import sys
    from awsglue.transforms import *
    from awsglue.utils import getResolvedOptions
    from pyspark.context import SparkContext
    from awsglue.context import GlueContext
    from awsglue.job import Job

except Exception as e:
    print(f'Error: {e})

# Create spark session
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session

job = Job(glueContext)
job.init(args['<JOB_NAME>'].args)


...

job.commit()

```

### Classes
- DynamicFrame Class: same as SparkSQL DataFrame
- DynamicReader Class
  - from catalog
  ```py
  data_source = glueContext
                    .create_dynamic_frame
                    .from_catalog(
                        database='flights-db',
                        table_name='flightscsv',
                        transformation_ctx='data_source'
                    )
  ```

  - `from_options(connection_type, connection_options)`: get DynamicFrame using specified connection and format

    ```py
    # examples for writing to S3
    df1 = glueContext
            .write_dynamic_frame
            .from_options(
                frame=df,
                connection_type='s3',
                connection_options={'path':'s3://xxx'},
                format='parquet',
                transformation_ctx='df1'
            )
    
    df1 = glueContext
            .write_dynamic_frame
            .from_options(
                frame=df,
                connection_type='s3',
                connection_options={
                    'path':'s3://xxx',
                    'compression':'gzip'
                },
                format='json',
                transformation_ctx='df1'
            )
    ```

  - `from_rdd(data, name, schema=None, sampleRatio=None)`: get DynamicFrame from RDD
- DynamicFrameWriter Class
  - `from_options(frame, connection_type, connection_options)`: write to DynamicFrame using specified connection and format
  - `from_catalog(frame, name_space, table_name, redshift_tmp_dir, transformation_ctx)`: Writes to tables in catalog
  - `from_jdbc_conf(frame, catalog_connection, connection_options, redshift_tmp_dir, transformation_ctx)`: Writes based on JDBC connection

### Glue Transforms

```py
# ApplyMapping Class
_ = ApplyMapping.apply(
    frame= datasource(),
    mappings=[
        {'show_id':}
    ]
)


```