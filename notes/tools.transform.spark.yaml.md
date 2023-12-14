---
id: inepmdk4jzri5yq1kp3u1fv
title: yaml
desc: ''
updated: 1697106148023
created: 1697104262039
---

## Working with YAML files

### Read YAML Data

Reads YAML data from a file or directory and returns a DataFrame.

``` py
df = spark.read.yaml("path/to/yaml/file")
```

### Write YAML Data

Writes the DataFrame content to a YAML file.

``` py
df.write.yaml("path/to/save/yaml/file")
```

### Convert YAML to DataFrame

If you already have YAML data as a string or a variable, you can convert it into a DataFrame using the `spark.createDataFrame()` function.

``` py
yaml_data = """

- name: John
  age: 25
- name: Jane
  age: 30
"""
df = spark.createDataFrame(yaml_data, "string", samplingRatio=1.0)
```

### Convert DataFrame to YAML

To convert a DataFrame to YAML format, you can use the `DataFrame.toJSON()` and `RDD.saveAsTextFile()` functions.

``` py
yaml_data = df.toJSON().collect()
rdd = spark.sparkContext.parallelize(yaml_data)
rdd.saveAsTextFile("path/to/save/yaml/file")
```

### Parsing YAML Arrays

If your YAML data contains arrays, you can use the `get_yaml_object() function` to parse and extract the elements of the array. This function takes a YAML string and returns an array of elements.

``` py
from pyspark.sql.functions import expr

# Sample input (YAML)

yaml_data = """

- name: John
  age: 25
  hobbies:
  - hiking
  - reading
- name: Jane
  age: 30
  hobbies:
  - swimming
  - painting
"""

# Parse and extract hobbies

df = spark.createDataFrame([(1, yaml_data)], ["id", "yaml"])
df = df.withColumn("hobbies", expr("get_yaml_object(yaml, 'hobbies')"))

# Output DataFrame

df.show(truncate=False)
```

###### Output

``` raw
+---+----------------+
|id |hobbies         |
+---+----------------+
|1  |[hiking, reading]|
+---+----------------+
```

### Handling Nested YAML Data

If your YAML data contains nested structures, you can use the `from_yaml()` function to parse and extract the nested elements. This function takes a YAML string and returns a nested structure.

``` py
from pyspark.sql.functions import from_yaml

# Sample input (YAML)

yaml_data = """

- name: John
  age: 25
  address:
    street: 123 Main St
    city: New York
- name: Jane
  age: 30
  address:
    street: 456 Elm St
    city: London
"""

# Parse nested YAML data

df = spark.createDataFrame([(1, yaml_data)], ["id", "yaml"])
df = df.withColumn("parsed_yaml", from_yaml("yaml"))

# Output DataFrame

df.show(truncate=False)
```

###### Output

``` raw
+---+----------------------------------+
|id |parsed_yaml                       |
+---+----------------------------------+
|1  |[[John, 25, [123 Main St, New York]], [Jane, 30, [456 Elm St, London]]]|
+---+----------------------------------+
```

### Exploding Nested YAML Data

If your YAML data contains nested structures and you want to flatten them, you can use the explode() function in conjunction with `from_yaml()` to explode the nested elements into separate rows.

``` py
from pyspark.sql.functions import explode

# Sample input (YAML)

yaml_data = """

- name: John
  age: 25
  hobbies:
  - hiking
  - reading
- name: Jane
  age: 30
  hobbies:
  - swimming
  - painting
"""

# Parse and explode nested YAML data
df = spark.createDataFrame([(1, yaml_data)], ["id", "yaml"])
df = df.withColumn("parsed_yaml", from_yaml("yaml"))
df = df.select("id", explode("parsed_yaml").alias("person"))

# Output DataFrame
df.show(truncate=False)
```

###### Output

```raw
+---+------------------------+
|id |person                  |
+---+------------------------+
|1  |[John, 25, [hiking, reading]]|
|1  |[Jane, 30, [swimming, painting]]|
+---+------------------------+
```

### Generate Schema from YAML files

``` py

import yaml
from pyspark.sql.types import StructType, StructField, IntegerType, StringType

# Function to Load YAML File
def load_yaml_file(yaml_file_path):
    with open(yaml_file_path, 'r') as file:
        schema_yaml = yaml.safe_load(file)
    return schema_yaml

# Function to Generate PySpark Schema from YAML Data
def generate_schema_from_yaml(yaml_data):
    fields = yaml_data['fields']
    schema = StructType()
    
    for field in fields:
        if field['type'] == 'integer':
            schema.add(StructField(field['name'], IntegerType(), True))
        elif field['type'] == 'string':
            schema.add(StructField(field['name'], StringType(), True))
    
    return schema


from pyspark.sql import SparkSession

spark = SparkSession.builder.master("local[1]").appName('SparkByExamples.com').getOrCreate()

# Load the schema from the YAML file
yaml_file_path = "your_yaml_file_path_here.yaml"
yaml_data = load_yaml_file(yaml_file_path)
schema = generate_schema_from_yaml(yaml_data)

# Load data using the generated schema
data = [("1", "John Doe", "30", "123 Main St"), ("2", "Jane Doe", "25", "234 Elm St")]
df = spark.createDataFrame(data, schema=schema)

df.show()
```