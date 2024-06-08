---
id: lnp5yzvb5whqlez5lwe88wz
title: basics
desc: ''
updated: 1717294614556
created: 1697783930285
---
- [[tools.transform.spark.basics.hadoop]]
- [[tools.transform.spark.basics.data_storage]]
- [[tools.transform.spark.basics.tables]]
- [[tools.transform.spark.basics.transform]]
- [[tools.transform.spark.basics.execution]]
- [[tools.transform.spark.basics.api]]

---

<https://www.freecodecamp.org/news/deep-dive-into-spark-internals-and-architecture-f6e32045393b/>

## Apache Spark

### Spark DataFrames

#### How SQL is executed?

- To parse the SQL qyery, when SQL is executed, metadata store is referenced to check for syntax errors
- The database will read data from the .dbf file, which stores the table data to process it accordingly and show the results

---

### Data Processing in Spark

### SQL Table vs DataFrame

- A table consists of:
  - storage layer: stores the data in a file
  - metadata layer: stores the table schema and other impt info
  - compute layer: presents you with a database table on which SQL is executed on
- Analysis Exception error is thrown if metadata is not available
- In Spark, there are two forms of tables: Spark SQL Tables and Spark DataFrames

#### SQL Table

- Schema Information is stored in the metadata store
- Table and metadata are presistent objects and visible across applications
  - When a table is created and data is loaded, the data is stored internally in the data files
  - Available formats include CSV, JSON, Parquet, AVRO, XML since Spark supports structured, semi-structured and unstructured data
  - Spark storage layer also supports distributed storage for e.g. HDFS, Cloud Storage i.e. Amazon S3, Azure ADLS
- Tables are created with a predefined table schema
- Tables supports SQL Expressions and does not support DataFrame API

#### Spark DataFrame

- Schema Information is stored in runtime catalog
- DataFrame and Catalog are runtime objects and live only during runtime. DataFrame is visible to your app only.
- DataFrame supports schema-on-read
- DataFrame offers APIs, does not support SQl

### Creating DataFrames

``` py
# spark is the SparkSession object
# FYI, Builder Design Pattern is used here
fire_df = spark.read
            .format('csv')
            .option('header', 'true')
            .option('inferSchema', 'true')
            .load(<file.csv>)

# Alternatively,
fire_df = spark.read.csv(<file.csv>, header="true",inferSchema="true")

display(fire_df.show())

# Convert dataframe to table
fire_df.createGlobalTempView(<table_name>)

%sql select * from global_temp.<table_name>
```

### Creating SQL Tables

``` sql
-- db will appear on the data tab
CREATE database if not exists demo_db;
CREATE table if not exists deml_tbl(
    <col_name> <col_dtype>
    ...
) using parquet;
INSERT INTO demo_tbl
SELECT * FROM global_temp.<table_name>;

```

---

### Creating Spark Apps

#### Structure of a stream processing job

  1. Read the data
  2. Transform based on business requirements
  3. Write rhe output

#### Reading the data

``` py

def load_survey_df(spark, data_file):
    return spark.read \
        .option("header", "true") \
        .option("inferSchema", "true") \
        .csv(data_file)

if __name__ == "__main__":
  # check if src file exists
  if len(sys.argv) != 2:
      logger.error("Usage: HelloSpark <filename>")
      sys.exit(-1)

  # read data
  survey_raw_df = load_survey_df(spark, sys.argv[1])

```

So, I am going to ask for the user input here.
this line will make sure that our program does not finish and we can look at the Spark UI, right?
This line is for local debugging and not for production.

### The Runtime Architecture

- Apache Spark is a distributed computing platform.
- Every Spark application is a distributed application in itself.
- To run a distributed application will require a cluster

### The Cluster

- A cluster is a pool of physical computers.
- For example, to have a cluster, you need:
  - Multiple machines of which each machine, for e.g. comes with 16 CPU cores and 64 GB RAM.
  - They are networked managed using the Hadoop YARN cluster manager.
  - The entire pool is termed as a cluster, and individual machines are known as worker nodes.
  - Given ten workers, each with 16 CPU cores and 64 GB RAM, my total CPU capacity is 160 CPU cores and the RAM capacity is 640 GB.
- Clusters come in many forms
  - local cluster on your local machine for development and unit testing
  - production cluster
    - Two most commonly used cluster for Spark are the Hadoop YARN cluster and Kubernetes Cluster which covers more than 90% market share.
    - Others include Mesos and Spark Standalone cluster

#### Running a Spark app on a cluster

- Steps to run a Spark application on a cluster.
  1. use the spark-submit command to submit my spark application to the cluster.
  2. The request goes to the YARN resource manager.
  3. The YARN RM will create one Application Master container on a worker node
  4. Starts my application's main() method in the container.

### The Container

- The container is an isolated virtual runtime environment.
- It get allocated with some CPU and memory allocation, based on what's available on the node.
  - For e.g., YARN RM gave 4 CPU Cores and 16 GB memory to this container (which is a portion of what's available i.e. 16 CPU cores and 64 GB of memory) and started it on a worker node.
- The application's main() method will run in the container and it can use the allocated 4 CPU cores and 16 GB memory

### The Application

- Py4j: <https://www.py4j.org/getting_started.html>

#### Background

- Spark Core was written in Scala which is a JVM language and it runs in the Java virtual machine (JVM).
- In order to make it accessible to Python developers, a Python wrapper was created on top of the Java API that was built on the Scala Spark Core.
  - The Python wrapper is known as PySpark.
  - Py4J is a popular library which is integrated within PySpark and allows Python to dynamically interface with JVM (Java Virtual Machine) objects.
  - The Java API runs Scala code in the JVM
    - The JVM application here is my Application Driver.
      - The PySpark main method is my PySpark Driver.

#### PySpark application

- A PySpark driver program begins by instantiating a SparkContext; either directly or indirectly using a SparkSession:
- In the driver program, pyspark.SparkContext executes spark-submit in a subprocess (yes, that spark-submit) in to  initialise a local Spark JVM process:
- Before executing spark-submit, a temporary file is created and it's name is exported as an environment variable:
- Subsequently, spark-submit instantiates a PythonGatewayServer to initialise a Py4J server and write the Py4J server connection details to this file:
- The Python driver can then read the contents of the file to establish a Py4J gateway to enable communication between the Python driver and the local Spark JVM process:

- So I have Python code in my main() method.
This python code is designed to start a Java main() method internally.
So my PySpark application will start a JVM application.
Once we have a JVM application, the PySpark wrapper will call the Java Wrapper using the Py4J connection.

And that's how PySpark works.

It will always start a JVM application and call Spark APIs in the JVM.
The actual Spark application is always a Scala application running in the JVM.
But PySpark is calling Java Wrapper using Py4J,
and the Java Wrapper runs Scala code in the JVM.

What do we call these two things?
These two terms are critical to remember.

So your Spark application driver is the main method of your application.

I told you that your Spark application is a distributed application in itself.
Your application driver distributes the work to others.
So the driver does not perform any data processing work but will instead create some executors and get the work done from them.

After starting, the driver will go back to the YARN RM and ask for some more containers.
The RM will create some more containers on worker nodes and give them to the driver.

So let's assume we got four new containers, each with 4 CPU Cores and 16 GB of memory.
Now the driver will start spark executor in these containers.
Each container will run one Spark executor, and the Spark executor is a JVM application.
So your driver is a JVM application, and your executor is also a JVM application.

The driver will assign work to the executors, monitor them and manage the overall application, but the executors do all the data processing.
So let's quickly revise some terminologies
---

You have a container, also known as Application Master or AM Container, that runs the driver.
If you submitted the PySpark code, you would have a PySpark driver and a JVM driver which will communicate using Py4J.
If you started a Scala or a Java application, you would have a JVM driver only.

The driver will start first, and then it will request the Cluster RM for more containers.
On receiving new containers, the driver will start executors in these new containers. We call them executor containers.
The AM container, as well as the executor containers, will run on the worker nodes.

Your worker node may have a physical CPU and Memory and your driver and executor can use a portion of that CPU and Memory that was given to the container. They cannot use extra CPU or Memory from the Workers.

If you are using Spark Dataframe API in Scala or Java, your runtime architecture looks like this.

You will have one JVM driver and one or more JVM executors.
If you are using PySpark Dataframe APIs, your runtime architecture looks like this.
You will have one PySpark driver, one JVM driver, and one or more JVM executors.

But if you are also using some additional Python libraries that are not part of the PySpark,

then your runtime architecture looks like this.

Even if you are creating UDFs in Python, your runtime architecture will look like this.

So what is the difference?

You have a Python worker at each executor.

What is a Python Worker, and Why do we need them?
Python worker is a Python runtime environment.
And you need them only if you are using some python specific code or libraries.
PySpark is a wrapper on Java code.
So as long as you are using only PySpark, you do not need a Python runtime environment.
All the PySpark code is translated into Java code, and it runs in the JVM.

But if you are using some Python libraries which doesn't have a Java wrapper,
you will need a Python runtime environment to run them.
So the executors will create a Python runtime environment so they can execute your Python code.

I will talk more about this in a later video.

But for now, let's remember that you may have Python workers inside the executor container

for running custom python code outside the PySpark API.
