---
id: pb0aft8gte924o25mr8kse2
title: task_groups
desc: ''
updated: 1710694457732
created: 1705449193472
---


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

#### Defining task groups in DAGs

1. `TaskGroup` class to create a task group context.
2. `@task_group` decorator on a Python function.
    - Dynamically map over a task group is possible only when using `@task_group`.

#### Task group parameters

-   You can use parameters to customize individual task groups.
-   Most important parameters are
    -   `group_id`: name of task group
    -   `default_args`: passed to all tasks in the task group.

#### `task_id` in task groups

-   When your task is within a task group, your callable `task_id` will be group_id.task_id so that `task_id` is unique across the DAG.
-   Important to this format when referring to specific tasks when working with XComs or branching.
-   To disable this behavior, set the task group parameter `prefix_group_id=False`.

#### Passing data through task groups

- With `@task_group`, you can pass data through the task group just like with regular @task decorators
- Things to consider when passing info into and out of task groups:
    - If downstream tasks require the output of tasks that are in the task group decorator, then the task group function must return a result.
    - If your task group function returns an output that another task takes as an input, Airflow can infer the task group and task dependency with the TaskFlow API. If your task group function's output isn't used as a task input, you must use the bit-shift operators (<< or >>) to define downstream dependencies to the task group.

    ```py
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

#### Nesting task groups

- Nest task groups by defining a task group indented within another task group. There is no limit to how many levels of nesting you can have.

    ```py
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

-   As of Airflow 2.5, you can use dynamic task mapping with the @task_group decorator to dynamically map over task groups. The following DAG shows how you can dynamically map over a task group with different inputs for a given parameter.

```py
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

-   If you use the same patterns of tasks in several DAGs or Airflow instances, it may be useful to create a custom task group class module.
-   To do so, you need to inherit from the TaskGroup class and then define your tasks within that custom class.
-   You also need to use self to assign the task to the task group.
-   Other than that, the task definitions will be the same as if you were defining them in a DAG file.

    ```py
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

-   In the DAG, you import your custom TaskGroup class and instantiate it with the values for your custom arguments:

    ```py
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

        tg1 = MyCustomMathTaskGroup(
            group_id="my_task_group", 
            num1=get_num_1(), 
            num2=19)

        @task
        def downstream_task():
            return "hello"

        tg1 >> downstream_task()


    custom_tg()
    ```