---
id: it0yq7981sds2nckak4fbs7
title: metadata_driven_framework
desc: ''
updated: 1697831396235
created: 1697830685160
---

<https://medium.com/@saraswataditya0035/spark-metadata-driven-framework-part-2-141d7b7eb587>

### File Structure

```md
├── reader
    ├── __init__.py
    ├── context.py
    ├── contract/
    │   ├── __init__.py
    │   ├── base_reader.py
    │   ├── batch_reader.py
    │   ├── stream_reader.py
    └── runner.py

```

### Config File

``` yaml
# config,yaml
reader:
  groceries_ds:
    type: "csv_batch"
    path: 'D:\Workspace\Kaggle\Grocery\Groceries_dataset.csv'
    header: true
    infer_schema: true
    view_name: "vw_groceries"
transformation:
  add_current_timestamp_col:
    type: "sql_step"
    query: "select *,current_timestamp() as ingested_date from vw_groceries "
    view_name: "vw_add_current_timestamp"

  add_is_active_col:
    type: "sql_step"
    query: "select *,'1' as is_active from vw_add_current_timestamp"
    view_name: "vw_add_is_active"
  add_partitioned_col:
    type: "sql_step"
    query: "select *,year(ingested_date) as year,month(ingested_date) as month from vw_add_current_timestamp"
    view_name: "vw_add_partitioned"
writer:
  writer_1:
    write_view_name: "vw_add_partitioned"
    type: "delta_batch"
    path: 'D:\Workspace\Kaggle\Grocery_Delta'
    mode: "append"
    partition_by: [ "year","month" ]

```

### Reader

This component is responsible for reading the data from the various data source like csv, json, delta etc.

``` py

from abc import ABC


class BaseReader(ABC):
    """
    Base Reader class for all the readers
    """
    NAME = "base_reader"

    @classmethod
    def get_name(cls):
        return cls.NAME


class BatchReader(BaseReader):
    NAME = "batch_reader"

    @abstractmethod
    def read(self):
        pass        


class StreamReader(BaseReader):
    __NAME = "stream_reader"

    @abstractmethod
    def read_stream(self):
        pass
```

### Helper Classes

```py
# context.py represent a data class which act as context class which deal with cross cutting concerns like logger, spark session provider etc.

class Context:
    def __init__(self, spark):
        self.__spark = spark

    @property
    def spark(self):
        return self.__spark

    def __enter__(self):
        pass

    def __exit__(self, exc_type, exc_val, exc_tb):
        return

# runner.py
# Here runner.py file represent the executor class for reader component i.e responsible for creating instance of the child reader class (inherit batch_reader or stream_reader), pass the options associated with that class and call the corresponding read or read_stream method to read the input data source, and register the returned dataframe into the spark catalog.

from mdd.core.reader.context import Context
from mdd.core.reader.contract.batch_reader import BatchReader
from mdd.core.reader.contract.stream_reader import StreamReader
import os
from glob import glob
import importlib.util
import sys
import inspect


class Runner:

    def __init__(self, spark, config):
        self.__spark = spark
        self.__config = config
        self.__source_mapping = Runner.create_mapping()

    def run(self):
        with Context(self.__spark) as ctx:
            for source_identifier, source_config in self.__config.items():
                source_name = source_config.get("type", None)
                source_type = source_name.split("_")[-1]
                sources = self.__source_mapping.get(source_type, None)
                if sources:
                    source = sources.get(source_name, None)
                    if source:
                        Runner.run_source(source, ctx, source_config)
                    else:
                        file = source_config.get("module", None)
                        if file:
                            module = Runner.get_module(file)
                            for name, obj in inspect.getmembers(module,
                                                                lambda cls: inspect.isclass(
                                                                    cls) and not inspect.isabstract(cls)):
                                if issubclass(obj, BatchReader):
                                    source_type = obj.get_name()
                                    self.__source_mapping['batch'][source_type] = obj
                                elif issubclass(obj, StreamReader):
                                    source_type = obj.get_name()
                                    self.__source_mapping['stream'][source_type] = obj
                            source = sources.get(source_name, None)
                            if source:
                                Runner.run_source(source, ctx, source_config)
                        else:
                            raise Exception(f"{source_type} is not defined")
                else:
                    raise Exception("No Source is registered into batch and stream.Please create some implementation")

    @staticmethod
    def run_source(source, ctx, source_config):
        source_obj = source(ctx, **source_config)
        if issubclass(source, BatchReader):
            source_obj.read()
        elif issubclass(source, StreamReader):
            source_obj.read_stream()

    @staticmethod
    def get_module(file):
        name = os.path.splitext(os.path.basename(file))[0]
        spec = importlib.util.spec_from_file_location(name, file)
        module = importlib.util.module_from_spec(spec)
        sys.modules[name] = module
        spec.loader.exec_module(module)
        return module

    @staticmethod
    def create_mapping():
        inbuilt_source_mapping = {'batch': {}, 'stream': {}}
        for file in glob(os.path.join(os.path.dirname(os.path.abspath(__file__)),
                                      "../../connector/reader/implementation", "*.py")):
            module = Runner.get_module(file)
            for name, obj in inspect.getmembers(module,
                                                lambda cls: inspect.isclass(cls) and not inspect.isabstract(cls)):
                if issubclass(obj, BatchReader):
                    source_type = obj.get_name()
                    inbuilt_source_mapping['batch'][source_type] = obj
                elif issubclass(obj, StreamReader):
                    source_type = obj.get_name()
                    inbuilt_source_mapping['stream'][source_type] = obj
        return inbuilt_source_mapping

```

# Implementation

``` py

from mdd.core.reader.contract.batch_reader import BatchReader
from pyspark.sql import SparkSession


class CsvBatchReader(BatchReader):
    NAME = "csv_batch"

    def __init__(self, ctx, **kwargs):
        self.__ctx = ctx
        self.__path = kwargs.get("path", None)
        self.__seperator = kwargs.get("seperator", ",")
        self.__quote = kwargs.get("quote", "\"")
        self.__header = kwargs.get("header", False)
        self.__infer_schema = kwargs.get("infer_schema", False)
        self.__schema = kwargs.get("schema", None)
        self.__view_name = kwargs.get("view_name", None)
        self.__validate()

    def __validate(self):
        if not self.__view_name:
            raise Exception("view_name is mandatory for csv reader")
        if not self.__path:
            raise Exception("Path is mandatory for csv reader")
        if not self.__infer_schema and self.__schema:
            raise Exception("Provide either inferSchema as True or Provide Schema String for csv reader")

    def read(self):
        spark = SparkSession.getActiveSession()
        reader = (spark
                  .read.format("csv")
                  .option("sep", self.__seperator)
                  .option("quote", self.__quote)
                  .option("header", self.__header)
                  .option("inferSchema", self.__infer_schema)
                  )
        if self.__schema:
            reader = reader.option("schema", self.__schema)
        reader_df = reader.load(self.__path)
        reader_df.createOrReplaceTempView(self.__view_name)
        return reader_df

from mdd.core.reader.contract.stream_reader import StreamReader
from pyspark.sql import SparkSession


class DeltaStreamReader(StreamReader):
    NAME = "delta_stream"

    def __init__(self, ctx, **kwargs):
        self.__ctx = ctx
        self.__path = kwargs.get("path", None)
        self.__ignore_deletes = kwargs.get("ignoreDeletes", None)
        self.__ignore_changes = kwargs.get("ignoreChanges", None)
        self.__starting_version = kwargs.get("startingVersion", None)
        self.__starting_timestamp = kwargs.get("startingTimestamp", None)
        self.__with_event_time_order = kwargs.get("withEventTimeOrder", None)
        self.__view_name = kwargs.get("view_name", None)
        self.__validate()

    def __validate(self):
        if not self.__view_name:
            raise Exception("view_name is mandatory for delta reader")
        if not self.__path:
            raise Exception("Path is mandatory for delta reader")
        if self.__starting_version and self.__starting_timestamp:
            raise Exception("Provide either startingVersion or startingTimestamp for delta reader not both")
        if self.__ignore_changes and self.__ignore_deletes:
            raise Exception("Provide either ignoreDeletes or ignoreChanges for delta reader not both")

    def read_stream(self):
        spark = SparkSession.getActiveSession()
        reader = spark.readStream.format("delta")
        if self.__ignore_deletes:
            reader = reader.option("ignoreDeletes", self.__ignore_deletes)
        if self.__ignore_changes:
            reader = reader.option("ignoreChanges", self.__ignore_changes)
        if self.__starting_version:
            reader = reader.option("startingVersion", self.__starting_version)
        if self.__starting_timestamp:
            reader = reader.option("startingTimestamp", self.__starting_timestamp)
        if self.__with_event_time_order:
            reader = reader.option("withEventTimeOrder", self.__with_event_time_order)
        reader_df = reader.load(self.__path)
        reader_df.createOrReplaceTempView(self.__view_name)
        return reader_df        
```
