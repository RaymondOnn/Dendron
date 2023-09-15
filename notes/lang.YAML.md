---
id: j4xuccg0lrj7h2xg7dw7l36
title: yaml
desc: ''
updated: 1694733050284
created: 1694602245194
---
https://www.linode.com/docs/guides/yaml-anchors-aliases-overrides-extensions/

## Anchors and Aliases

- The alias abbreviates YAML content, compacting it down so it takes up fewer bytes in a file system. 
- More importantly, human readers have less to take in and thus focus more effectively on the essentials of the definition. 
- Moreover, these anchor-alias combinations can ease maintenance chores. 
- Suppose MYSQL_USER needs to be updated from wordpress to special_wordpress_account: while naive YAML requires editing the MYSQL_USER in each of its uses–presumably the same as the number of databases in all environments
- The rewritten YAML only needs an update to its one anchor. 
- Each alias then properly receives the updated special_wordpress_account automatically. 
- Fewer distinct values to copy-and-paste inevitably mean fewer opportunities for inadvertent error.

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

## Overrides

- Sometimes segments of a YAML file share only part of their contents. - For e.g.The WordPress example might configure databases that are identical except that each instance has a distinct password.

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