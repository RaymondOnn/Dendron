---
id: ga5fmq1difno93owk5w3ckk
title: sensors
desc: ''
updated: 1701119506659
created: 1694568888098
---

## Sensors

- Waits for condition to be met
- Important Parameters:
  - poke_interval (default: 60secs)
  - timeout (default: 7 days)
- Good practice to always define the timeout for the sensor

## PostgresOperator

```py
from airflow.providers.http.sensors.http import HttpSensor


task = HttpSensor(
    task_id = <UNIQUE_TASK_NAME>,
    http_conn_id='user_api',
    endpoint= <INSERT_API_ENDPOINT>
)

```

