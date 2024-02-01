---
id: pp7fza9kon2cb5gadz38803
title: Docker
desc: ''
updated: 1702549478439
created: 1694576142162
---


https://www.restack.io/docs/airflow-faq-howto-docker-compose-index-07

## Running Airflow using Docker

- [Docs: Running Airflow in Docker](https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html)
``` bash
# create airflow folder directory
mkdir airflow-docker
mkdir -p ./dags ./logs ./plugins ./config

# download latest airflow docker-compose.yaml
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.7.1/docker-compose.yaml'

# running airflow init process
docker-compose up airflow-init
# launching all containers
docker-compose up


# log in airflow webserver dashboard @ localhost:8080
# username: airflow, pass: airflow

```


## Install Python dependencies in Airflow Docker Container
- Two ways: Image Extending, Image Customising

![Alt text](image-2.png)

### Image Extending
Step 1. Create `requirements.txt` file
```raw
scikit-learn==0.24.2
matplotlib==3.3.3
```
Step 2. Create `dockerfile`
```sh
FROM apache/airflow:2.0.1
COPY requirements.txt /requirements.txt
RUN pip install --user --upgrade pip
RUN pip install --no-cache-dir --user -r /requirement.txt
```
Step 3. Build extended image
```sh
docker build 
    .  # dockerfile loation -> current dorectory 
    --tag extending_airflow:latest # set image_name to extending_airflow
```
Step 4. Ensure image_name in `docker-compose.yaml` matches
Step 5: Build and run docker container
```
docker-compose up 
    -d 
    --no-deps 
    --build airflow-webserver 
    airflow-scheduler
```

### Example using python dependencies

```py
from datetime import datetime, timedelta

from airflow import DAG
from airflow.operators.python import PythonOperator


default_args = {
    'owner': 'coder2j',
    'retry': 5,
    'retry_delay': timedelta(minutes=5)
}


def get_sklearn():
    import sklearn
    print(f"sklearn with version: {sklearn.__version__} ")


def get_matplotlib():
    import matplotlib
    print(f"matplotlib with version: {matplotlib.__version__}")


with DAG(
    default_args=default_args,
    dag_id="dag_with_python_dependencies_v03",
    start_date=datetime(2021, 10, 12),
    schedule_interval='@daily'
) as dag:
    task1 = PythonOperator(
        task_id='get_sklearn',
        python_callable=get_sklearn
    )
    
    task2 = PythonOperator(
        task_id='get_matplotlib',
        python_callable=get_matplotlib
    )

    task1 >> task2
```