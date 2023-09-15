---
id: gcm07ahnhr634dbmi7v9f7n
title: Airflow
desc: ''
updated: 1694576155945
created: 1691960726557
---
## Links
- Tutorial: https://www.youtube.com/watch?v=K9AnJ9_ZAXE&ab_channel=coder2j
- Debug Tips: https://www.youtube.com/watch?v=5QxqqeOxJhI&list=PLwFJcsJ61oujAqYpMp1kdUBcPG0sE0QMT&index=15&ab_channel=coder2j
- Email Alerts: https://www.youtube.com/watch?v=D18G7hW8418&list=PLwFJcsJ61oujAqYpMp1kdUBcPG0sE0QMT&index=18&ab_channel=coder2j
- Bad & Best Practices:  https://www.youtube.com/watch?v=LBoT1DzGFS4&ab_channel=PlainSchwarz
- Trigger DAGs via REST API: https://brocktibert.com/post/trigger-airflow-dags-via-the-rest-api/





## Airflow Connections


### Postgres Operator
- NOTE: for host field, if using docker application, `localhost` doesn't work, try `host.docker.internal` instead
- TIP: Recommended to delete data before inserting data to prevent duplication of primary keys


```py
from datetime import datetime, timedelta

from airflow import DAG
from airflow.providers.postgres.operators.postgres import PostgresOperator


default_args = {
    'owner': 'coder2j',
    'retries': 5,
    'retry_delay': timedelta(minutes=5)
}


with DAG(
    dag_id='dag_with_postgres_operator_v03',
    default_args=default_args,
    start_date=datetime(2021, 12, 19),
    schedule_interval='0 0 * * *'
) as dag:
    task1 = PostgresOperator(
        task_id='create_postgres_table',
        postgres_conn_id='postgres_localhost',
        sql="""
            create table if not exists dag_runs (
                dt date,
                dag_id character varying,
                primary key (dt, dag_id)
            )
        """
    )

    # the curly bracket notation is for airflow macros
    task2 = PostgresOperator(
        task_id='insert_into_table',
        postgres_conn_id='postgres_localhost',
        sql="""
            insert into dag_runs (dt, dag_id) values ('{{ ds }}', '{{ dag.dag_id }}')
        """
    )

    task3 = PostgresOperator(
        task_id='delete_data_from_table',
        postgres_conn_id='postgres_localhost',
        sql="""
            delete from dag_runs where dt = '{{ ds }}' and dag_id = '{{ dag.dag_id }}';
        """
    )
    task1 >> task3 >> task2
```


