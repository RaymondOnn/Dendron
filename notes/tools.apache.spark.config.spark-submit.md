---
id: cbmwg0a4hua8bcex7y2o3k7
title: spark-submit
desc: ''
updated: 1698785605207
created: 1698785505852
---

## `spark-submit`
### What is `spark-submit`
- The spark-submit is a command-line tool that allows you to submit the Spark application to the cluster.
- General structure of the spark-submit command.

  ``` sh
  # example with the most commonly used options.
  spark-submit 
      --class <main-class> 
      --master <MASTER_URL>
      --deploy-mode <DEPLOY_MODE>
      <application-jars>   # pyspark script here
      [application-args] 
  ```

### Some important submit options

- Most commonly used options.
  ``` sh
  - class: Not applicable to Pyspark. For Java / Scala
  - master: YARN, local[3]
  - deploy mode: client OR cluster
  - conf: allows you to set add. spark config for e.g. spark.executor.memoryOverhead=0.20
  - driver-cores: Number of CPU cores for driver container i.e. 2
  - driver-memory: Amt of RAM for the driver container i.e. 8G 
  - num-executors: set required number of executors i.e. 4
  - executor-cores: 4
  - executor-memory: 16G
  ```
