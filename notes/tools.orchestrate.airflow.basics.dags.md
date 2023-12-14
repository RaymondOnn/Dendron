---
id: 87sot479s7690brjx52dsk2
title: dags
desc: ''
updated: 1701288446673
created: 1694576048906
---

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

### Testing DAG Tasks

```bash
# get airflow container details
docker-compose ps

# start bash terminal for airflow scheduler
docker exec -it <AIRFLOW_SCHEDULER_NAME> /bin/bash

# --------- In airflow scheduler terminal --------------------
# see all commands
airflow -h

# testing task
# Best Practice: Test task every time a new task is added
airflow tasks test <DAG_ID> <TASK_ID> <DATETIME_IN_THE_PAST>



```

### Airflow Task Flow API

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

### Xcom

-   Xcom, an abbreviation of “cross-communication”, lets you pass messages between tasks.
-   Although tasks are suggested to be atomic and not to share resources between them, sometimes exchanging messages between them become useful.
-   Xcom has two side; pusher and puller.
    -   The pusher sends a message and the puller receives the message.

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

### TaskGroups

-   Airflow task groups are a tool to organize tasks into groups within your DAGs.
-   Using task groups allows you to:
    -   Organize complicated DAGs, visually grouping tasks that belong together in the Airflow UI Grid View.
    -   Apply default_args to sets of tasks, instead of at the DAG level using DAG parameters.
    -   Dynamically map over groups of tasks, enabling complex dynamic patterns.
    -   Turn task patterns into modules that can be reused across DAGs or Airflow instances.

    ```py
    # TaskGroup Method
    from airflow.utils.task_group import TaskGroup

    t0 = EmptyOperator(task_id='start')

    # Start task group definition
    with TaskGroup(
        group_id='my_task_group'
        default_args={"conn_id": "postgres_default"},
        tooltip="This task group is also very important!",
        prefix_group_id=True,
        # parent_group=None,
        # dag=None,
        # add_suffix_on_collision=True, # resolves group_id collisions by adding a suffix
    ) as tg1:
        t1 = EmptyOperator(task_id='task_1')
        t2 = EmptyOperator(task_id='task_2')

        t1 >> t2
    # End task group definition

    t3 = EmptyOperator(task_id='end')

    # Set task group's (tg1) dependencies
    t0 >> tg1 >> t3


    # @task_group Method
    from airflow.decorators import task_group

    t0 = EmptyOperator(task_id='start')

    # Start task group definition
    @task_group(
        group_id='my_task_group'
        default_args={"conn_id": "postgres_default"},
        tooltip="This task group is very important!",
        prefix_group_id=True,
        # parent_group=None,
        # dag=None,
    )
    def tg1():
        t1 = EmptyOperator(task_id='task_1')
        t2 = EmptyOperator(task_id='task_2')

        t1 >> t2
    # End task group definition

    t3 = EmptyOperator(task_id='end')

    # Set task group's (tg1) dependencies
    t0 >> tg1() >> t3
    ```

1. Defining Task Groups
    - There are two ways to define task groups in your DAGs:
        - Use the `TaskGroup` class to create a task group context.
        - Use the `@task_group` decorator on a Python function.
    - Dynamically map over a task group is possible only when using `@task_group`.
2. Task group parameters
    - You can use parameters to customize individual task groups.
    - The two most important parameters are
    - `group_id`: name of task group
    - `default_args`: passed to all tasks in the task group.
3. `task_id` in task groups
   - When your task is within a task group, your callable task_id will be group_id.task_id. 
   - This ensures the task_id is unique across the DAG. 
   - It is important that you use this format when referring to specific tasks when working with XComs or branching. 
   - You can disable this behavior by setting the task group parameter `prefix_group_id=False`. 
1. Passing data through task groups
   - When you use the `@task_group` decorator, you can pass data through the task group just like with regular @task decorators
   - There are a few things to consider when passing information into and out of task groups:
     - If downstream tasks require the output of tasks that are in the task group decorator, then the task group function must return a result. 
     - If your task group function returns an output that another task takes as an input, Airflow can infer the task group and task dependency with the TaskFlow API. If your task group function's output isn't used as a task input, you must use the bit-shift operators (<< or >>) to define downstream dependencies to the task group.
        ``` py
        from airflow.decorators import dag, task, task_group
        from pendulum import datetime
        import json


        @dag(start_date=datetime(2023, 8, 1), schedule=None, catchup=False)
        def task_group_example():
            @task
            def extract_data():
                data_string = '{"1001": 301.27, "1002": 433.21, "1003": 502.22}'
                order_data_dict = json.loads(data_string)
                return order_data_dict

            @task
            def transform_sum(order_data_dict: dict):
                total_order_value = 0
                for value in order_data_dict.values():
                    total_order_value += value

                return {"total_order_value": total_order_value}

            @task
            def transform_avg(order_data_dict: dict):
                total_order_value = 0
                for value in order_data_dict.values():
                    total_order_value += value
                    avg_order_value = total_order_value / len(order_data_dict)

                return {"avg_order_value": avg_order_value}

            @task_group
            def transform_values(order_data_dict):
                return {
                    "avg": transform_avg(order_data_dict),
                    "total": transform_sum(order_data_dict),
                }

            @task
            def load(order_values: dict):
                print(
                    f"""Total order value is: {order_values['total']['total_order_value']:.2f} 
                    and average order value is: {order_values['avg']['avg_order_value']:.2f}"""
                )

            load(transform_values(extract_data()))


        task_group_example()
        ```
1. Nest task groups
   - Nest task groups by defining a task group indented within another task group. There is no limit to how many levels of nesting you can have.
    ``` py
    # Using TaskFlow API
    groups = []
    for g_id in range(1,3):
        @task_group(group_id=f"group{g_id}")
        def tg1():
            t1 = EmptyOperator(task_id="task1")
            t2 = EmptyOperator(task_id="task2")

            sub_groups = []
            for s_id in range(1,3):
                @task_group(group_id=f"sub_group{s_id}")
                def tg2():
                    st1 = EmptyOperator(task_id="task1")
                    st2 = EmptyOperator(task_id="task2")

                    st1 >> st2
                sub_groups.append(tg2())

            t1 >> sub_groups >> t2
        groups.append(tg1())

    groups[0] >> groups[1]
    
    # Traditional syntax
    groups = []
    for g_id in range(1,3):
        with TaskGroup(group_id=f"group{g_id}") as tg1:
            t1 = EmptyOperator(task_id="task1")
            t2 = EmptyOperator(task_id="task2")

            sub_groups = []
            for s_id in range(1,3):
                with TaskGroup(group_id=f"sub_group{s_id}") as tg2:
                    st1 = EmptyOperator(task_id="task1")
                    st2 = EmptyOperator(task_id="task2")

                    st1 >> st2
                    sub_groups.append(tg2)

            t1 >> sub_groups >> t2
            groups.append(tg1)

    groups[0] >> groups[1]
    ```

#### Generate task groups dynamically at runtime
- As of Airflow 2.5, you can use dynamic task mapping with the @task_group decorator to dynamically map over task groups. The following DAG shows how you can dynamically map over a task group with different inputs for a given parameter.

``` py
# dynamically maps over the task group group1 with 6 different inputs for the my_num parameter

from airflow.decorators import dag, task_group, task
from pendulum import datetime


@dag(
    start_date=datetime(2022, 12, 1),
    schedule=None,
    catchup=False,
)
def task_group_mapping_example():
    # creating a task group using the decorator with the dynamic input my_num
    @task_group(group_id="group1")
    def tg1(my_num):
        @task
        def print_num(num):
            return num

        @task
        def add_42(num):
            return num + 42

        print_num(my_num) >> add_42(my_num)

    # a downstream task to print out resulting XComs
    @task
    def pull_xcom(**context):
        pulled_xcom = context["ti"].xcom_pull(
            # reference a task in a task group with task_group_id.task_id
            task_ids=["group1.add_42"],
            # only pull Xcom from specific mapped task group instances (2.5 feature)
            map_indexes=[2, 3],
            key="return_value",
        )

        # will print out a list of results from map index 2 and 3 of the add_42 task
        print(pulled_xcom)

    # creating 6 mapped task group instances of the task group group1 (2.5 feature)
    tg1_object = tg1.expand(my_num=[19, 23, 42, 8, 7, 108])

    # setting dependencies
    tg1_object >> pull_xcom()


task_group_mapping_example()
```

#### Custom task group classes
- If you use the same patterns of tasks in several DAGs or Airflow instances, it may be useful to create a custom task group class module. 
- To do so, you need to inherit from the TaskGroup class and then define your tasks within that custom class. 
- You also need to use self to assign the task to the task group. 
- Other than that, the task definitions will be the same as if you were defining them in a DAG file.
    ``` py
    from airflow.utils.task_group import TaskGroup
    from airflow.decorators import task


    class MyCustomMathTaskGroup(TaskGroup):
        """A task group summing two numbers and multiplying the result with 23."""

        # defining defaults of input arguments num1 and num2
        def __init__(self, group_id, num1=0, num2=0, tooltip="Math!", **kwargs):
            """Instantiate a MyCustomMathTaskGroup."""
            super().__init__(group_id=group_id, tooltip=tooltip, **kwargs)

            # assing the task to the task group by using `self`
            @task(task_group=self)
            def task_1(num1, num2):
                """Adds two numbers."""
                return num1 + num2

            @task(task_group=self)
            def task_2(num):
                """Multiplies a number by 23."""
                return num * 23

            # define dependencies
            task_2(task_1(num1, num2))
    ```

- In the DAG, you import your custom TaskGroup class and instantiate it with the values for your custom arguments:
    ``` py
    from airflow.decorators import dag, task
    from pendulum import datetime
    from include.custom_task_group import MyCustomMathTaskGroup


    @dag(
        start_date=datetime(2023, 8, 1),
        schedule=None,
        catchup=False,
        tags=["@task_group", "task_group"],
    )
    def custom_tg():
        @task
        def get_num_1():
            return 5

        tg1 = MyCustomMathTaskGroup(group_id="my_task_group", num1=get_num_1(), num2=19)

        @task
        def downstream_task():
            return "hello"

        tg1 >> downstream_task()


    custom_tg()
    ```