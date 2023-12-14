---
id: 6o63drx5uws6jini042nsmr
title: compose
desc: ''
updated: 1701057101249
created: 1700034678596
---

https://itnext.io/a-beginners-guide-to-deploying-a-docker-application-to-production-using-docker-compose-de1feccd2893

### Compose

-   Docker Compose is a tool to automate the running of docker containers.
-   It uses a YAML file to configure your application’s services and creates all the app's services from that configuration.
-   Great for development, testing, CI workflows, and staging environments.
-   `docker-compose --help` when any help is needed with the cli commands

### `docker-compose.yml`

-   a YAML file that provides configuration of the services as well as networks, volumes, environment variables, etc. to be used by the services (their containers).
-   General structure

    ```yaml
    version: "X"

    services:
        web:
            build: .
            ports:
                - "5000:5000"
            volumes:
                - .:/code
        redis:
            image: redis
    ```

-   In essence, it is similar to converting the `docker run` command into yaml

<table>
  <tr>
    <th>the <code>docker run</code> command</th>
    <th>the <code>`docker-compose.yaml`</code> file</th>
  </tr>
  <tr>
    <td>

```shell
docker run
  -d
  --name mongodb
  -p 27017:27017
  -e MONGO-INITDB_ROOT_USERNAME=admin
  -v name:/var/lib/mysql/data
  --net mongo-network
  mongo # image name
```

</td>
<td>

-   docker compose takes care of creating a common network

```yaml
version:'3'

services:
  mongodb:          # maps from --name mongodb
    image: mongo    # maps from image name
    ports:
       - 27017:37017 # maps from -p 27017:27017 (HOST:CONTAINER)
 volumes:
  - db-data:/var/lib/mysql/data
    environment:
       MONGO...USERNAME=admin # maps from -e MONGO-INITDB_ROOT_USERNAME=admin
volumes:
 db-data (named volumes)
  driver: local
```

</td>
  </tr>
</table>

-   Note that there are different versions for the Compose YAML format
    -   A particular Compose file version is compatible with the particular Docker Engine version(s), [here](https://docs.docker.com/compose/compose-file/compose-versioning/) is the compatibility matrix.
    -   A typical Compose file version is in the form of <major>:<minor> release. If we omit the :<minor> part, Docker Compose automatically assumes the minor version
    -   TIP: Start first with version 2, but move on the newer versions i.e. 3, 3.1 when you need some new features
-   While `docker-compose.yaml` is default filename, but any name can be used with `docker-compose -f`

-   YAML file can be used with `docker-compose` for docker automation
-   NOTE: App versions in Docker
    -   Every app with dependencies, will also have version requirements for those dependencies.
    -   Therefore, when building your Dockerfile and docker-compose.yml file, remember that you'll need to check the compatible versions in that apps documentation.

#### Using Environment Variables

-   An environment variable can be used to substitute variables in docker-compose.yml file.
-   This is quite useful while targeting environment-specific configuration changes. Let’s see a small example.

```yaml
# ${NGINX_VERSION}, ${NGINX_WWW_DIR} and the $NGINX_PORT values provided
# using the environment variables of the same name.

# Compose file version
version: "3.9"

# services
services:
    # frontend web service
    frontend:
        image: nginx:${NGINX_VERSION:-latest} # set to latest if variable not available
        volumes:
            - "${NGINX_WWW_DIR}:/usr/share/nginx/html/"
        ports:
            - "$NGINX_PORT:80"
```

-   Multiple ways to provide environment variables to a running service (container). Here's the precedence for the different ways
    1. Value from the compose file (environment)
    2. Value from the shell environment (of container)
    3. Environment (.env) file (env_file)
    4. Dockerfile (ENV instruction)
    5. Value is not defined

##### The `environment` attribute

-   Using the Compose environment option allows us to declare environment variables and their values inside our Compose file, like this:

    ```yaml
    version: "3.1"

    services:
    db:
        container_name: Mongo-db
        image: mongo:latest
        restart: always
        volumes:
            - ./myData:/data/db
        environment:
            - MONGO_INITDB_DATABASE=MyDatabase
            - MONGODB_USER=Nya
            - MONGODB_PASS=secretpassword
        ports:
            - 27020:27017
    ```

-   This is the easiest, quickest way of storing environment variables inside Compose files.
-   However, storing the values of your environment variables directly in the Compose file is a huge security risk.

##### The `.env` file

-   .env files are plain text files, which are used for configuration. FYI files with name beginning with a `.` remain hidden to the system.
-   .env files must be created at the root of your project, i.e. where your `docker-compose.yml` file should be.
-   Useful if you have multiple environment variables you need to store.
-   We declare and assign variables in our .env file. You can name the variables however you want.
    ```raw
    # .env file
    NGINX_WWW_DIR=./www
    NAME_WITH_QUOTES="John Doe"
    ```
-   To assign values using the `.env` variables, we use `${VARIABLE_NAME}`

    ```yaml
    version: "3.1"

    services:
    db:
        container_name: Mongo-db
        image: mongo:latest
        restart: always
        volumes:
            - ./myData:/data/db
        environment:
            - MONGO_INITDB_DATABASE=${DATABASE}
            - MONGODB_USER=${USERNAME}
            - MONGODB_PASS=${PASS}
        ports:
            - 27020:27017
    ```
- TIP: You can check which values are assigned to the environment variables by running the following command (in a different terminal):
    ``` bash
    docker-compose config
    ```

##### The `env_file` option
- Adds environment variables to the container based on the file content.
- The advantage of this method is that you can store the file anywhere and name it appropriately, for example, `.env.ci`, `.env.dev`, `.env.prod`

##### Variable Substitution
- `${VARIABLE:-default}`: default if VARIABLE is unset or empty in the environment.
- `${VARIABLE-default}`: default only if VARIABLE is unset in the environment.
- For Required Variables
  - `${VARIABLE:?err}` exits with an error message containing err if VARIABLE is unset or empty in the environment.
  - `${VARIABLE?err}` exits with an error message containing err if VARIABLE is unset in the environment.
#### Build Arguments

-   Build arguments are the build-time environment variables.
-   These will be available only while building the image from a Dockerfile.
-   We specify a build argument using the ARG instruction in the Dockerfile.
    ```raw
    # Dockerfile
    ARG tag=latest
    FROM node:$tag
    ARG working_directory
    WORKDIR $working_directory
    ```
-   These can be defined with a default value `ARG=VAL` in the Dockerfile or just with the name.
-   While building an image, we use the --build-arg <key>=<val> flag with the `docker build` command to override or provide values of these variables.
-   In the docker-compose.yml, we provide these values using the args field of the build object. You can either provide a fixed value to a build argument or substitute its value from the environment variable.

```yaml
# Compose file version
version: "3.9"

# services
services:
    # frontend web service
    frontend:
        build:
            context: ./frontend
            args:
                - tag: 12.10.0
                - working_directory: $FRONTEND_WORDIR
                - log_level
```

#### Running the `docker-compose.yaml`

docs: <https://docs.docker.com/engine/reference/commandline/compose_up/>

```sh
# starting the containers
docker-compose
  -f <docker-compose.yaml>
  -d            # detached mode
  --no-deps     # Don’t start linked services
  --build       # Build images before starting containers
  up

# stopping the containers.The common network is stopped as well
docker-compose
  -f <docker-compose.yaml>  # name of docker-compose file
  -v                       # remove volumes
  down
```

#### Using multiple `docker-compose.yaml` files

-   Docker Compose lets you merge and override a set of Compose files together to create a composite Compose file.
-   By default, Compose reads two files, a `compose.yml` and an optional `compose.override.yml` file.
    -   By convention, the `compose.yml` contains your base configuration.
    -   The override file can contain configuration overrides for existing services or entirely new services.
-   If a service is defined in both files, Compose merges the configurations
    -   For single-value options like `image`, `command` or `mem_limit`: the new value replaces the old value.
    -   For the multi-value options `ports`, `expose`, `external_links`, `dns`, `dns_search`, and `tmpfs`: Compose concatenates both sets of values
    -   In the case of `environment`, `labels`, `volumes`, and `devices`, Compose "merges" entries together with locally defined values taking precedence
-   To use multiple override files, or an override file with a different name, you can use the -f option to specify the list of files.
    -   Compose merges files in the order they're specified on the command line.

##### Example

-   A common use case for multiple files is changing a development Compose app for a production-like environment (which may be production, staging or CI).
-   To support these differences, you can split your Compose configuration into a few different files:

1. Start with a base file that defines the canonical configuration for the services.

`compose.yml`

```yaml
services:
    web:
        image: example/my_web_app:latest
        depends_on:
            - db
            - cache

    db:
        image: postgres:latest

    cache:
        image: redis:latest
```

`compose.override.yml`

```yaml
# In this example the development configuration exposes some ports to the host, mounts our code as a volume, and builds the web image.
services:
    web:
        build: .
        volumes:
            - ".:/code"
        ports:
            - 8883:80
        environment:
            DEBUG: "true"

    db:
        command: "-d"
        ports:
            - 5432:5432

    cache:
        ports:
            - 6379:6379
```

2. When you run `docker compose up` it reads the overrides automatically.

3. To use this Compose app in a production environment, another override file is created, which might be stored in a different git repo or managed by a different team.

`compose.prod.yml`

```yaml
services:
    web:
        ports:
            - 80:80
        environment:
            PRODUCTION: "true"

    cache:
        environment:
            TTL: "500"
```

To deploy with this production Compose file you can run

```bash
$ docker compose -f compose.yml -f compose.prod.yml up -d
```

This deploys all three services using the configuration in compose.yml and compose.prod.yml but not the dev configuration in compose.override.yml.

#### The `Include` Option

```yaml
include:
    - my-compose-include.yaml #with serviceB declared
services:
    serviceA:
        build: .
        depends_on:
            - serviceB #use serviceB directly as if it was declared in this Compose file
```

-   `Include` let you divide your `docker-compose.yml` file into smaller, easier-to-manage chunks.
-   Used to define the dependency on another Compose application, or sub-domain.
-   Supported in Docker Desktop 4.22 and later, also Docker Compose 2.20 and later.
-   You can directly include a different Compose file in your local Compose file by using the include top-level element. The relative path problem that extends and merges present is resolved this way.

`mysql-docker-compose.yml`

```yaml
version: "3.7"

services:
    mysql:
        container_name: mysql
        image: mysql
        command: --default-authentication-plugin=mysql_native_password
        hostname: mysql
        restart: always
        volumes:
            - $PWD/volume/mysql/data:/var/lib/mysql
        ports:
            - "3306:3306"
        environment:
            - MYSQL_PASSWORD=admin
            - MYSQL_ROOT_PASSWORD=admin
            - MYSQL_VERSION=latest
```

`docker-compose.yml`

```yaml
version: "3.7"

include:
    - ./mysql-docker-compose.yml

services:
    redis:
        image: redis
        restart: always
        hostname: redis
        container_name: redis
        ports:
            - "6379:6379"
        volumes:
            - $PWD/volume/redis/data:/data
        depends_on:
            - mysql
    elasticsearch:
        image: elasticsearch:7.17.8
        ports:
            - "9200:9200"
        volumes:
            - "$PWD/volume/elasticsearch/data:/usr/share/elasticsearch/data"
        environment:
            - discovery.type=single-node
        ulimits:
            memlock: { soft: -1, hard: -1 }
        mem_limit: 1g
    rabbitmq:
        image: rabbitmq:3-management
        restart: always
        container_name: rabbitmq
        hostname: rabbitmq
        environment:
            RABBITMQ_ERLANG_COOKIE: "cookie"
            RABBITMQ_DEFAULT_VHOST: "/"
            RABBITMQ_DEFAULT_USER: "guest"
            RABBITMQ_DEFAULT_PASS: "guest"
        ports:
            - "5672:5672"
            - "15672:15672"
        healthcheck:
            test: ["CMD", "rabbitmqctl", "status"]
            interval: 100s
            timeout: 10s
            retries: 10
        tty: true
        volumes:
            - "$PWD/volume/rabbitmq/data:/data"
    adminer:
        image: adminer:4.6.3
        restart: always
        environment:
            ADMINER_DESIGN: "pappu687"
        ports:
            - 8080:8080
volumes:
    redis_data:
        driver: local
    rabbitmq_data:
        driver: local
```

`run-compose.sh`

```bash
#!/bin/bash
docker pull mysql:5.7
docker pull rabbitmq:3-management
docker pull redis

mkdir -p volume

if [ ! -d "$PWD/volume/elasticsearch" ]; then
   mkdir -p volume/elasticsearch/data
   chmod 777 -R $PWD/volume/elasticsearch/
fi

if [ ! -d "$PWD/volume/mysql" ]; then
   mkdir -p volume/mysql/data
   chmod 777 -R $PWD/volume/mysql
fi

mkdir -p volume/rabbitmq/data
mkdir -p volume/redis/data


if [ -z $1 ]; then
  chmod 777 -R $PWD/volume/
  docker-compose -f docker-compose.yml up -d
elif [[ $1 = "up" ]]; then
  chmod 777 -R $PWD/volume/
elif [[ $1 = "down" ]]; then
  docker-compose -f docker-compose.yml down
fi
```

### CLI Commands

Here are the most common Docker Compose commands that we can use with our files.

-   `docker-compose`: Every Compose command starts with this command. You can also use docker-compose <command> `--help` to provide additional information about arguments and implementation details.
    ```bash
    docker compose --help
    >>> Define and run multi-container applications with Docker.
    ```
    -   `build`: Builds or rebuild images in the `docker-compose.yml` file.
    -   `images`: List the images built using the current docker-compose file.
    -   `stop`: Stops the running containers of specified services.
    -   `run`: Create containers from images built for the services mentioned in the compose file.
    -   `up`: i.e.`docker-compose build` + `docker-compose run`. Builds the images if they are not located in cache locally and starts the containers. If images are already built, it will fork the container directly.
    -   `ps`: List all the containers in the current docker-compose file, running or stopped.
    -   `down`: This command is similar to the docker system prune command. However, in Compose, it stops all the services and cleans up the containers, networks, and images.

#### Scaling docker compose services

-   You can easily increase or decrease the number of container instances for a particular service. Here’s how you can scale services using Docker Compose:
    ```bash
    docker compose up --scale <SVC_NAME>=<N_CONTAINERS>
    ```
-   Docker Compose will start the specified number of container instances for the scaled service.
    -   If the service depends on other services, Docker Compose will also create the necessary instances for those dependencies.
    -   if you already have containers running for the service, Docker Compose will create additional instances to match the desired scale. It will not remove any existing containers unless explicitly instructed.
-   Scaling allows you to distribute the workload across multiple instances of a service, which can improve performance and handle increased traffic or demand.
-   However it’s important to consider the resource requirements and capacity of your host machine when scaling services.
