---
id: q8so1gxvn91xihcbx1daool
title: Dev_env
desc: ''
updated: 1694543864029
created: 1694543382430
---
https://dev.to/digitaldisorder/how-to-develop-apache-airflow-dags-in-docker-compose-95m
- [Best Practices](https://airflow.apache.org/docs/apache-airflow/stable/best-practices.html#configuration)

## `docker-compose.yaml`

```yaml
version: '2.1'
services:
    postgres:
        image: postgres:9.6
        environment:
            - POSTGRES_USER=airflow
            - POSTGRES_PASSWORD=airflow
            - POSTGRES_DB=airflow
    webserver:
        image: puckel/docker-airflow:1.10.9
        restart: always
        mem_limit: 2048m
        depends_on:
            - postgres
        env_file:
            - .env
        environment:
            - LOAD_EX=n
            - EXECUTOR=Local
        volumes:
            - ./dags:/usr/local/airflow/dags
            - ./test:/usr/local/airflow/test
            - ./plugins:/usr/local/airflow/plugins
            # Uncomment to include custom plugins
            - ./requirements.txt:/requirements.txt
            - ~/.aws:/usr/local/airflow/.aws
        ports:
            - "8080:8080"
        command: webserver
        healthcheck:
            test: ["CMD-SHELL", "[ -f /usr/local/airflow/airflow-webserver.pid ]"]
            interval: 30s
            timeout: 30s
            retries: 3
```
<br>

- we allow to pass custom environment variables straight from the dotenv file (best practice is not include it in the files)
- we will use postgres instance running as another docker container
- we share our dags/test/plugins directories with the host so we can just edit our code on our local machine and run all the tests in container