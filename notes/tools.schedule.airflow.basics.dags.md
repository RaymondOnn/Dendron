---
id: 87sot479s7690brjx52dsk2
title: dags
desc: ''
updated: 1711149097755
created: 1694576048906
---

`airflow dags report`
`airflow dags reserialize`

### DAG Essential Configs

```py
from airflow import DAG
from datatime import datetime

with DAG(
    dag_id=<UNIQUE_DAG_NAME>,
    start_date=datetime(YYYY,MM,DD),
    schedule_interval=<CRON_EXPRESSION>,
    catchup=False
)
```

-   **start_date**: The timestamp from which the scheduler will attempt to backfill
-   **schedule_interval**: How often a DAG run
-   **end_date**: The timestamp from which a DAG ends

### Creating DAGs

```py
from datetime import datetime, timedelta

from airflow import DAG
from airflow.operators.bash import BashOperator


default_args = {
    'owner': 'coder2j',
    'retries': 5,
    'retry_delay': timedelta(minutes=2)
}


with DAG(
    dag_id='our_first_dag_v5',
    default_args=default_args,
    description='This is our first dag that we write',
    start_date=datetime(2021, 7, 29, 2),
    schedule_interval='@daily'
) as dag:
    task1 = BashOperator(
        task_id='first_task',
        bash_command="echo hello world, this is the first task!"
    )

    task2 = BashOperator(
        task_id='second_task',
        bash_command="echo hey, I am task2 and will be running after task1!"
    )

    task3 = BashOperator(
        task_id='thrid_task',
        bash_command="echo hey, I am task3 and will be running after task1 at the same time as task2!"
    )

    # Task dependency method 1
    # task1.set_downstream(task2)
    # task1.set_downstream(task3)

    # Task dependency method 2
    # task1 >> task2
    # task1 >> task3

    # Task dependency method 3
    task1 >> [task2, task3]
```

<br>



### Task Flow API

-   cleaner implementation of DAGs
-   helps with managing inputs and outputs between tasks

```py

from datetime import datetime, timedelta
from airflow.decorators import dag, task


default_args = {
    'owner': 'coder2j',
    'retries': 5,
    'retry_delay': timedelta(minutes=5)
}

@dag(dag_id='dag_with_taskflow_api_v02',
     default_args=default_args,
     start_date=datetime(2021, 10, 26),
     schedule_interval='@daily')
def hello_world_etl():

    @task(multiple_outputs=True)
    def get_name():
        return {
            'first_name': 'Jerry',
            'last_name': 'Fridman'
        }

    @task()
    def get_age():
        return 19

    @task()
    def greet(first_name, last_name, age):
        print(f"Hello World! My name is {first_name} {last_name} "
              f"and I am {age} years old!")

    name_dict = get_name()
    age = get_age()
    greet(first_name=name_dict['first_name'],
          last_name=name_dict['last_name'],
          age=age)

greet_dag = hello_world_etl()

```

**Looking at the Airflow UI**

![Alt text](airflow_task_flow_api_example.png)

#### Mixing TaskFlow API with traditional operators

```py
from airflow.decorators import dag
from airflow.operators.bash import BashOperator
from pendulum import datetime


@dag(start_date=datetime(2022, 8, 1), schedule=None, catchup=False)
def bash_two_commands_example_dag():
    @task
    def start():
        print('Workflow started..')

    @task
    def end():
        print('Workflow ended...')

    say_hello_and_create_a_secret_number = BashOperator(
        task_id="say_hello_and_create_a_secret_number",
        bash_command="echo Hello $MY_NAME! && echo $A_LARGE_NUMBER | rev  2>&1\
                     | tee $AIRFLOW_HOME/include/my_secret_number.txt",
        env={"MY_NAME": "<my name>", "A_LARGE_NUMBER": "231942"},
        append_env=True,
    )

    start >> say_hello_and_create_a_secret_number >> end


bash_two_commands_example_dag()
```

<br>

> #### Loading the DAG files
>
> -   the `DagFileProcessorProcess` is responsible for loading DAGs as Python modules, governed by a configuration parameter called `dagbag_import_timeout`
>
> ##### `DagFileProcessorProcess`
>
> -   A process in the Scheduler that continuously monitors the `DAG_FOLDER` for new files and changes to existing files.
> -   When a new or updated Python file is detected, the process imports the DAG file as a Python module, executing the file's Python code.
> -   Any DAG objects that are instantiated during this process are automatically added to the DagBag.
>
> ##### `dagbag_import_timeout`
>
> -   a config parameter in the `airflow.cfg` file that specifies the max amount of time (in seconds) the scheduler is allowed to spend on parsing and importing each individual DAG file.
> -   If a file takes longer than this amount of time to import, the import is aborted and an error is logged.
> -   The default value for `dagbag_import_timeout` is 30 seconds.
> -   You can adjust this value in the `airflow.cfg` file under the [core] section:
>     ```
>     [core]
>     dagbag_import_timeout = 60 # increase timeout to 60s
>     ```
> -   See [[tools.schedule.airflow.basics.dags.chain]] for solutions


### Trigger rules
- By default, a task is triggered only when **all upstream tasks have succeeded**. 
- You can use trigger rules to change this default behavior.
- The following options are available:
    - `all_success`: (default) The task runs only when all upstream tasks have succeeded.
    - `all_failed`: The task runs only when all upstream tasks are in a failed or upstream_failed state.
    - `all_done`: The task runs once all upstream tasks are done with their execution.
    - `all_skipped`: The task runs only when all upstream tasks have been skipped.
    - `one_failed`: The task runs when at least one upstream task has failed.
    - `one_success`: The task runs when at least one upstream task has succeeded.
    - `one_done`: The task runs when at least one upstream task has either succeeded or failed.
    - `none_failed`: The task runs only when all upstream tasks have succeeded or been skipped.
    - `none_failed_min_one_success`: The task runs only when all upstream tasks have not failed or upstream_failed, and at least one upstream task has succeeded.
    - `none_skipped`: The task runs only when no upstream task is in a skipped state.
    - `always`: The task runs at any time.

``` py
import random
from airflow.decorators import dag, task
from airflow.operators.empty import EmptyOperator
from datetime import datetime
from airflow.utils.trigger_rule import TriggerRule  # <--- See Here


@dag(start_date=datetime(2021, 1, 1), max_active_runs=1, schedule=None, catchup=False)
def branching_dag():
    # EmptyOperators to start and end the DAG
    start = EmptyOperator(task_id="start")
    end = EmptyOperator(task_id="end", trigger_rule=TriggerRule.ONE_SUCCESS)   # <--- See Here

    # Branching task
    @task.branch
    def branching(**kwargs):
        branches = ["branch_0", "branch_1", "branch_2"]
        return random.choice(branches)

    branching_task = branching()

    start >> branching_task

    # set dependencies
    for i in range(0, 3):
        d = EmptyOperator(task_id="branch_{0}".format(i))
        branching_task >> d >> end


branching_dag()
```

### Xcom

-   XComs allow tasks to exchange task metadata or small amounts of data. They are defined by a key, value, and timestamp.
-   However, it is only designed to be used for very small amounts of data.
-   Xcom has two side; The pusher sends a message and the puller receives the message.
-   Tasks can be configured to push XComs by calling the `xcom_push()` method. Similarly, `xcom_pull()` can be used in a task to receive an XCom.
-   When an XCom is pushed, it is stored in the Airflow metadata database and made available to all other tasks.
-   Any time a task returns a value (for example, when your Python callable for your PythonOperator has a return), that value is automatically pushed to XCom.

```py

# Get modified files in a folder since the last execution time
pusher = BashOperator(
        task_id='get_files',
        bash_command='files=$(find \"$(cd .; pwd)\" -newermt {{ prev_ds }} ! -newermt {{ ds }}); echo $files',
        xcom_push=True,  # This will send stdout to Xcom.
        dag=dag)
# Pull Xcom from a task, "get_files"
puller = BashOperator(
        task_id='process_files',
        bash_command='python process.py --files \"{{ task_instance.xcom_pull(task_ids='get_files') }}\"',
        dag=dag)

```
