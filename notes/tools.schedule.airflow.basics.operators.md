---
id: 6wn4lc4p8fak8gwil7egm5h
title: operators
desc: ''
updated: 1705983694522
created: 1694545332850
---

## PostgresOperator

- NOTE: for host field, if using docker application, `localhost` doesn't work, try `host.docker.internal` instead
- TIP: Recommended to delete data before inserting data to prevent duplication of primary keys
```py
from airflow.providers.postgres.operators.postgres import PostgresOperator

task = PostgresOperator(
    task_id = <UNIQUE_TASK_NAME>,
    postgres_conn_id='postgres',
    sql= <INSERT_SQL_QUERY>
)
```

## SimpleHttpOperator


```py
from airflow.providers.http.operators.http import SimpleHttpOperator
import json

task = SimpleHttpOperator(
    task_id = <UNIQUE_TASK_NAME>,
    http_conn_id='user_api',
    endpoint= <INSERT_API_ENDPOINT>,
    method='GET',
    response_filter= lambda response: json.loads(response.text),
    log_response=True   # log reponses into logs
)

```

## PythonOperator

#### Passing Parameters

- use `op_kwargs` to load params:
  - e.g. `op_kwargs = {'name': 'Tom' , 'age': 20}` to pass params for `greet(name, age)`

#### Data Sharing

- NOTE: max size is 48KB
- use **Xcoms** to pass values across tasks
  - `return` publishes values to the 'general space' where other functions have access to and use
  - to publish value: use `ti.xcom_push(<KEY>, <VALUE>)`
  - to get values:
    - from 'general space':use `ti.xcom_pull(<task_id_of_task_to_provide_value>)`
    - from specific task:use `ti.xcom_pull(<task_id_of_task_providing_value>, <key>)`

```py

from datetime import datetime, timedelta

from airflow import DAG
from airflow.operators.python import PythonOperator

default_args = {
    'owner': 'coder2j',
    'retries': 5,
    'retry_delay': timedelta(minutes=5)
}

def greet(some_dict, ti):
    print("some dict: ", some_dict)
    first_name = ti.xcom_pull(task_ids='get_name', key='first_name')
    last_name = ti.xcom_pull(task_ids='get_name', key='last_name')
    age = ti.xcom_pull(task_ids='get_age', key='age')
    print(f"Hello World! My name is {first_name} {last_name}, "
          f"and I am {age} years old!")

def get_name(ti):
    ti.xcom_push(key='first_name', value='Jerry')
    ti.xcom_push(key='last_name', value='Fridman')

def get_age(ti):
    ti.xcom_push(key='age', value=19)

with DAG(
    default_args=default_args,
    dag_id='our_dag_with_python_operator_v07',
    description='Our first dag using python operator',
    start_date=datetime(2021, 10, 6),
    schedule_interval='@daily'
) as dag:
    task1 = PythonOperator(
        task_id='greet',
        python_callable=greet,
        op_kwargs={'some_dict': {'a': 1, 'b': 2}}
    )

    task2 = PythonOperator(
        task_id='get_name',
        python_callable=get_name
    )

    task3 = PythonOperator(
        task_id='get_age',
        python_callable=get_age
    )

    [task2, task3] >> task1
```
#### Checking results

```bash
# get airflow container details
docker-compose ps

# start bash terminal for airflow worker
docker exec -it <WORKER_CONTAINER_NAME> /bin/bash

# --------- In airflow worker terminal --------------------
# check folder contents
ls <folder_name>

# exit terminal. Shortcut: Ctrl + D
exit
```

## DockerOperator
- https://medium.com/towards-data-science/using-apache-airflow-dockeroperator-with-docker-compose-57d0217c8219

1. Build task image
    ``` dockerfile
    # docker image to run python script
    FROM python:3.10

    WORKDIR /usr/src/app
    RUN --mount=type=bind,source=./web2kafka/requirements.txt,target=/tmp/requirements.txt  \
        pip install --no-cache-dir -r /tmp/requirements.txt

    COPY ./web2kafka/src/ .

    CMD ["python", "./hello.py"]
    ```
    ``` bash
    # To build docker image
    docker build <WORKDIR> -t <TAG>

    # To run and test image
    docker run <IMG>
    ```
2. `docker-compose.yaml`
    1. add `docker.sock` to `volumes` 
    2. add the `apache-airflow-providers-docker` under `_PIP_ADDITIONAL_REQUIREMENTS`
        ``` yaml
        x-airflow-common:
            ...
            environment:
                _PIP_ADDITIONAL_REQUIREMENTS: ${_PIP_ADDITIONAL_REQUIREMENTS:- apache-airflow-providers-docker}    #<---See here
                ...
            volumes:
                - ./dags:/opt/airflow/dags
                - ./logs:/opt/airflow/logs
                - ./plugins:/opt/airflow/plugins
                - ./include:/opt/airflow/include
                - /var/run/docker.sock:/var/run/docker.sock  #<---See here
        ```
    3. add `docker-proxy` service
        ``` yaml
        # Be mindful of the networks 
        docker-proxy:
            image: bobrik/socat
            command: "TCP4-LISTEN:2375,fork,reuseaddr UNIX-CONNECT:/var/run/docker.sock"
            ports:
            - "2376:2375"
            volumes:
            - /var/run/docker.sock:/var/run/docker.sock
        ```
3. `dag.py` 

    ```py

    from airflow.providers.docker.operators.docker import DockerOperator
    from airflow.decorators import dag, task
    from airflow.utils.dates import days_ago


    @dag(start_date=days_ago(0), schedule=None, catchup=False)
    def web_streams():
        @task
        def start():
            print("Workflow started...")

        @task
        def end():
            print("Workflow done.")

        run_script = DockerOperator(
            task_id="web2kafka",
            image="task_web2kafka",
            api_version='auto',
            docker_url="tcp://docker-proxy:2375",  # <-- Rmb to add this!!
            auto_remove=True,
            command='echo "this is a test message shown from within the container"',
            mount_tmp_dir=False,
            # tty=True,
            network_mode="bridge"
        )

        start() >> run_script >> end()


    web_streams()

    ```