---
id: kbc5l0zw14tcd7h3zym36jo
title: Airflow
desc: ""
updated: 1705992473770
created: 1705992008318
---

### `spark_submit` via [[tools.orchestrate.airflow]]

-   ref: [link](https://stackoverflow.com/questions/53344285/is-there-a-way-to-submit-spark-job-on-different-server-running-master/53344713#53344713)

-   There are 3 ways you can submit Spark jobs using Apache Airflow remotely:

    1. Using `SparkSubmitOperator`:

        - This operator expects you have a spark-submit binary and YARN client config setup on our Airflow server. It invokes the spark-submit command with given options, blocks until the job finishes and returns the final status. Also streams the logs from the spark-submit command stdout and stderr.
        - You really only need to configure a yarn-site.xml file, I believe, in order for `spark-submit --master yarn --deploy-mode client` to work. Once an Application Master is deployed within YARN, then Spark is running locally to the Hadoop cluster.
        - If you really want, you could add a hdfs-site.xml and hive-site.xml to be submitted as well from Airflow (if that's possible), but otherwise at least hdfs-site.xml files should be picked up from the YARN container classpath

    2. Using `SSHOperator`:

        - Use this operator to run bash commands on a remote server (using SSH protocol via paramiko library) like spark-submit. The benefit of this approach is you don't need to copy the hdfs-site.xml or maintain any file.

    3. Using `SimpleHTTPOperator` with Livy:

        - Livy is an open source REST interface for interacting with Apache Spark from anywhere. You just need to have REST calls.
