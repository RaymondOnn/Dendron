---
id: 8uma60wziyv1blyhzd9cpyr
title: Dbfs
desc: ''
updated: 1666439619206
created: 1666323332649
---

## Access DBFS (Databricks File System)

The <a href="https://docs.databricks.com/data/databricks-file-system.html" target="_blank">Databricks File System</a> (DBFS) is a virtual file system that allows you to treat cloud object storage as though it were local files and directories on the cluster.

Run file system commands on DBFS using the magic command: **`%fs`**

```python
%fs mounts

%fs ls

# Check temp folder 
%fs ls dbfs:/tmp

# Writing into text file
%fs put dbfs:/tmp/FILL_IN.txt "This is a test of the emergency broadcast system, this is only a test" --overwrite=true

%fs help
```

Note: `%fs is` shorthand for the DBUtils module: `dbutils.fs`

```python

# Run file system commands on DBFS using DBUtils directly
files = dbutils.fs.ls("dbfs:/tmp")

# Visualize results in a table using the Databricks display function
# Docs for Display function: https://docs.databricks.com/notebooks/visualizations/index.html#display-function-1
display(files)

# Similarly, to view the temp file
file_name = "dbfs:/tmp/FILL_IN.txt"
contents = dbutils.fs.head(file_name)

print("-"*80)
print(contents)
print("-"*80)

```

## Accessing file paths stored in variable


<details>
<summary>Using Python</summary>

```python
files = dbutils.fs.ls(DA.paths.events)
display(files)
```
</details>
<details>
<summary>Using SQL</summary>

1. First, declare the python variable as a variable in the spark context which SQL commands can access:

```python
%python
spark.conf.set("whatever.events", DA.paths.events)
```
2. Next, run sql command using event file on DBFS
```sql
%sql
CREATE TABLE IF NOT EXISTS events
USING DELTA
OPTIONS (path = "${whatever.events}");
```
</details>