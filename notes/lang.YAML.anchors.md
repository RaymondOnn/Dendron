---
id: h6oi6zi9fj7ontg7ci6ggau
title: anchors
desc: ''
updated: 1702690698824
created: 1697103408897
---
<https://www.linode.com/docs/guides/yaml-anchors-aliases-overrides-extensions/>
<https://medium.com/@kinghuang/docker-compose-anchors-aliases-extensions-a1e4105d70bd>

## Anchors (`&`) and Aliases (`*`)

- Anchors let you identify an item and then reference it elsewhere
- Anchors are created using the `&` sign followed by an alias name
- We then use this alias later to reference the value using the `*` sign

### Why use them?  

- The alias abbreviates YAML content, compacting it down so it takes up fewer bytes in a file system.
- More importantly, human readers have less to take in and thus focus more effectively on the essentials of the definition.
- Moreover, these anchor-alias combinations can ease maintenance chores. Changes only need to be make at one location, the anchor
- Fewer distinct values to copy-and-paste inevitably mean fewer opportunities for inadvertent error.

### Examples

``` yaml
services:
  wordpress:
    image: wordpress:latest
    restart: &restart_policy always
    environment: & env_vars
      app: webservice
      env: production
  mysql:
    image: mysql: 3.8
    restart: *restart_policy
    environment: *env_vars

```

### Anchors for objects

<br>

<table>
  <tr>
    <th>before</th>
    <th>after</th>
  </tr>
  <tr>
    <td>

``` yaml
version: "3.9"

services:
  production-db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
  MYSQL_ROOT_PASSWORD: somewordpress
  MYSQL_DATABASE: wordpress
  MYSQL_USER: wordpress
  MYSQL_PASSWORD: wordpress
      ...
  test-db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
    MYSQL_ROOT_PASSWORD: somewordpress
    MYSQL_DATABASE: wordpress
    MYSQL_USER: wordpress
    MYSQL_PASSWORD: wordpress
```

</td>
<td>

- the `&database-definition` is an anchor to which the `*database-definition` alias refers.
  
``` yaml
version: "3.9"

services:
  production-db: &database-definition
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
  MYSQL_ROOT_PASSWORD: somewordpress
  MYSQL_DATABASE: wordpress
  MYSQL_USER: wordpress
  MYSQL_PASSWORD: wordpress
      ...
  test-db: *database-definition
```

</td>
  </tr>
</table>

### Extensions (`<<`)

- Extends the Anchor values to add additional values

``` yaml
services:
  wordpress:
    image: wordpress:latest
    restart: &restart_policy always
    environment: & env_vars
      app: webservice
      env: production
  mysql:
    image: mysql: 3.8
    restart: *restart_policy
    environment: 
      <<: *env_vars
      type: database

```

## Overrides

- Sometimes segments of a YAML file share only part of their contents.
- For e.g.The WordPress example might configure databases that are identical except that each instance has a distinct password.

``` yaml
version: "3.9"

services:
  production-db: &database-definition
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment: &environment-definition
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: production-password
      ...
  test-db:
    <<: *database-definition
    environment:
      <<: *environment-definition
      MYSQL_PASSWORD: test-password
      ...
```

.?
>?

```yaml
# docker-compose.yaml
message-server:
    ...
    restart: no            
product-server:
    ...
    restart: on-failure

```

## Execute Multiple Commands

``` yaml
version: "3.9"

services:
  DB:
    image: Postgres
    volumes:
      - ./data/db:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
  web:
    build: .
    command: >
      sh -c "
             python manage.py migrate &&
             python manage.py runserver 0.0.0.0:8080"
    volumes:

      - .:/tonyloi
    ports:
      - "8080:8080"
    environment:
      - POSTGRES_NAME=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    depends_on:
      - DB
```
---

``` yaml
# https://github.com/himewel/airflow_celery_workers/blob/main/docker-compose.yaml
version: '3.7'

x-connections: &commonConnections
    AIRFLOW__CORE__EXECUTOR: 'CeleryExecutor'
    AIRFLOW__CELERY__RESULT_BACKEND: 'db+postgresql://airflow:airflow@postgres:5432/airflow'
    AIRFLOW__CELERY__BROKER_URL: 'redis://redis:6379/1'

x-airflow-environment: &airflowEnvironment
    AIRFLOW__CORE__SQL_ALCHEMY_CONN: 'postgresql+psycopg2://airflow:airflow@postgres:5432/airflow'
    PYTHONPATH: '/opt/airflow/dags'
    AIRFLOW_HOME: '/opt/airflow'
    AIRFLOW__CORE__LOAD_EXAMPLES: 'TRUE'
    AIRFLOW__WEBSERVER__NAVBAR_COLOR: '#bca0dc'
    AIRFLOW__WEBSERVER__EXPOSE_CONFIG: 'TRUE'

x-default-user: &defaultUser
    USERNAME: 'admin'
    PASSWORD: 'admin'
    ROLE: 'Admin'
    FIRSTNAME: 'Firstname'
    LASTNAME: 'Lastname'
    EMAIL: 'username@domain.com'

services:
    postgres:
        image: postgres:12-alpine
        environment:
            POSTGRES_USER: airflow
            POSTGRES_PASSWORD: airflow
            POSTGRES_DB: airflow

    redis:
        image: redis:6.0.9-alpine

    scheduler:
        build:
            context: .
            dockerfile: Dockerfile
        depends_on:
            - postgres
        environment:
            <<: *commonConnections
            <<: *airflowEnvironment
        command: scheduler

    webserver:
        build:
            context: .
            dockerfile: Dockerfile
        depends_on:
            - postgres
            - scheduler
        environment:
            <<: *commonConnections
            <<: *airflowEnvironment
            <<: *defaultUser
        ports:
            - 8080:8080
        command: webserver

    flower:
        build:
            context: .
            dockerfile: Dockerfile
        depends_on:
            - scheduler
            - redis
        environment:
            <<: *commonConnections
            <<: *airflowEnvironment
            <<: *defaultUser
        ports:
            - 5555:5555
        command: flower

    worker:
        build:
            context: .
            dockerfile: Dockerfile
        environment:
            <<: *commonConnections
            <<: *airflowEnvironment
        command: worker


```