---
id: jpavt0sywu1pukuhxekihvw
title: Backfill
desc: ''
updated: 1694573096214
created: 1694572930303
---
## Catch-Up vs BackFill

#### Catchup

- Catchup is a parameter that controls the scheduling behavior of a workflow in Airflow.
- When the `catchup=True` (which is the default behavior), Airflow will automatically backfill any missed executions of the workflow for all the intervals between the workflow's start date and the current date.

#### Backfill

- Backfill refers to the process of running a workflow for a specific range of historical dates, filling in any gaps in the data processing for those dates.
- This can be useful when you have added a new task to an existing workflow, or when you need to reprocess data due to changes in the logic or data quality issues.

## Manually executing **BACKFILL**

Step 1: Set `catchup=False`

```py
from datetime import datetime, timedelta

from airflow import DAG
from airflow.operators.bash import BashOperator


default_args = {
    'owner': 'coder2j',
    'retries': 5,
    'retry_delay': timedelta(minutes=5)
}

with DAG(
    dag_id='dag_with_catchup_backfill_v02',
    default_args=default_args,
    start_date=datetime(2021, 11, 1),
    schedule_interval='@daily',
    catchup=False   # default to True
) as dag:
    task1 = BashOperator(
        task_id='task1',
        bash_command='echo This is a simple bash command!'
    )
```

Step 2: Run these commands in CLI

``` sh
# to get airflow docker container ID
docker ps

# start bash terminal 
docker exec -it <AIRFLOW_CONTAINER_ID> bash  

# execute backfill. date_format: YYYY_MM_DD
airflow dags backfill -s <START_DATE> -e <END_DATE> <DAG_ID> 

# exit bash terminal
exit
```
