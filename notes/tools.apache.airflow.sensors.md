---
id: ga5fmq1difno93owk5w3ckk
title: Sensors
desc: ''
updated: 1694569191070
created: 1694568888098
---

## Sensors

- Waits for condition to be met
- Important Parameters:
  - poke_interval (default: 60secs)
  - timeout (default: 7 days)

## PostgresOperator

```py
from airflow.providers.http.sensors.http import HttpSensor


task = HttpSensor(
    task_id = <UNIQUE_TASK_NAME>,
    http_conn_id='user_api',
    endpoint= <INSERT_API_ENDPOINT>
)

```