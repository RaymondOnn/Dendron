---
id: fvmrsttli05j0rjp97dbpus
title: executors
desc: ''
updated: 1702701673779
created: 1694576171029
---

## Executors

-   Defines how tasks are executed. Note that it does not do the execution of task
-   Many types i.e. local Executor, Celery, Kubernetes
-   Related Parameters
    -   executor:

### The default config

### The Sequential Executor

-   The sequential executor is the executor by default when you install airflow manually.
-   With this executor, the scheduler runs one task at a time.
-   Usually used only for making experiments or debugging issues.
-   To configure this executor, you just need to modify the executor setting with the sequential executor
    ```raw
    executor = SequentialExecutor
    ```

### The Local Executor
![Alt text](airflow_executors_local.png)
-   The local executor allows tasks to be executed in parallel on a single machine, by spawning processes in a controlled fashion.
-   Limited by the resources that that single machine has and thus, doesn't scale well
-   That said, it offers simplicity and ease of setup, making it a good choice for local small-scale development and testing scenarios where distributed computing is not required.


-   To configure this executable:
    ```raw
    executor=LocalExecutor
    sql_alchemy_conn=postgresql+psycopg2://<USER>:<PWD>@<HOST>/<DB>
    ```

### The Celery Executor
![Alt text](airflow_executors_celery.png)
-   The Celery Executor allows tasks to be executed in parallel on multiple worker machines, making it an excellent choice for heavy workloads.
-   Executors can be scaled up or down depending on the workload by adding more worker machine
-   To start a worker, you can use the command: `airflow celery worker`

#### Setting up celery executors

![Alt text](airflow_executor_celery_components.png)

-   This executor uses the Celery framework, an asynchronous distributed task queue that utilizes distributed message passing.
-   The Celery queue is composed of two parts:
    1.  the **result backend** where the airflow workers store the status of the tasks that have been executed
    2.  the **broker**, which is a queue where the scheduler sends the task to execute and the workers pull the tasks out of that queue to execute them.
-   Hence, we will need
    1. Celery is installed: `pip install 'apache-airflow[celery]'`
    2. For Celery to be configured properly, we also need:
        - a Celery backend (like RabbitMQ or Redis)
        - a result backend (like a relational database or caching system).
-   Configuration is done by specify these settings in `airflow.cfg`:

```raw
[core]
executor=CeleryExecutor

[celery]
celery_result_backend = db+postgresql://airflow:airflow@localhost/airflow

sql_alchemy_conn=postgresql+psycopg2://<USER>:<PWD>@<HOST>/<DB>
celery_result_backend=postgresql+psycopg2://<USER>:<PWD>@<HOST>/<DB>
celery_broker_url=redis://:@redis:6379/0  # redis
celery_broker_url=amqp://guest:guest@localhost:5672// # rabbitmq
```

#### the Celery Executor model

-   Once a DAG is triggered, the scheduler sends the tasks to the broker (queue)
-   Each worker will fetch the tasks from the broker in order to execute it.
-   Once execution is completed, the state of the task is stored into the result backend, which is the same database of airflow. You can use another database if you want.
-   The task is deemed completed.

#### Monitoring tasks with Flower

-   Monitoring the status of tasks can be done via the Flower monitoring tool for this purpose:

    ```bash
    airflow celery flower
    ```

    ![Alt text](airflow_flower_ui.png)

-   Flower is a web based tool used to manage and monitor celery clusters.
    -   It has a UI that monitors all the workers on celery.
    -   It gives clear statistics on the active tasks, processed tasks showing whether the task was successful or not, and also tells the load average of the tasks.
    -   It also maintains the name of the task, the arguments, the result, and the time taken to complete the task.
-   To access flower, use `docker compose --profile flower up -d` and go to http://localhost:5555

### [Understanding Airflow Queues](https://www.restack.io/docs/airflow-knowledge-apache-airflow-queue-management)

-   Apache Airflow's CeleryExecutor allows tasks to be distributed across multiple worker nodes.
-   By strategically using queues, you can optimize the execution of tasks across your Airflow cluster, ensuring that resources are used efficiently and tasks are executed in the appropriate environment.
-   Why?
    -   Dynamic Scaling: Use different queues for tasks with varying resource requirements to scale workers dynamically.
    -   Task Isolation: Isolate tasks by environment or resource needs by assigning them to specific queues.
-   Use Cases
    -   Resource Intensive Tasks: Assign to a dedicated queue with workers having appropriate resources.
    -   Environment Specific Tasks: Use queues to manage tasks that require specific runtime environments.

1. Queue Configuration
    - Default Queue: Set in `airflow.cfg` under `operators -> default_queue`, this queue is used when no other is specified.
    - Task Assignment: Use the `queue` attribute of BaseOperator to assign tasks to specific queues.
2. Worker Configuration

    - Listening to Queues: Workers can listen to one or more queues. Start a worker with `airflow celery worker -q queue1,queue2` to listen to multiple queues.
    - Specialized Workers: For tasks requiring specific resources or environments, dedicate workers to specialized queues.

    ```py
    # Assign a task to a specific queue
    my_task = MyOperator(
        task_id='task_with_specific_queue',
        queue='heavy_resource_queue',
        ...
    )
    ```
