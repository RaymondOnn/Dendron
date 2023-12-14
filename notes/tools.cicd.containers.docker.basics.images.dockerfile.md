---
id: p043fo2tlhsirtqsqjt4m9e
title: Dockerfile
desc: ''
updated: 1700034942568
created: 1699595658864
---

-   [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/#label)
-   <https://www.youtube.com/watch?v=C1GE07UEFDo&ab_channel=BretFisherDockerandDevOps>
-   <https://www.youtube.com/@Smacacademy/search?query=docker%20image%20build>

### What is Dockerfile

-   A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.
-   Helps with automation of the steps needed when manually building an image
-   Instructions in a Dockerfile are executed in a sequential order. Hence order of these commands matters.

<table>
  <tr>
    <th>Image Environment Blueprint</th>
    <th><code>Dockerfile</code></th>
  </tr>
  <tr>
    <td>

```raw
install node

set MONGO_DB_USERNAME=admin
set MONDO_DB_PWD=password

create /home/app folder

copy current folder files to /home/app

start the amp with: 'node server.js'
```

</td>
<td>

```text
FROM node:13-alpine  <IMAGE_NAME:VERSION>

# environment variables
ENV MONGO_DB_USERNAME=admin MONDO_DB_PWD=password

# RUN allows execution of any Linux command in the container environment
# note that the directory will live within the container environment
RUN mkdir -p /home/app

# COPY executes on the host machine
COPY . /home/app

# CMD is an entry point command
# you can have multiple RUN command but only one CMD command
CMD ['node', '/home/app/server.js']
```

  </td>
  </tr>
</table>

### Running the Dockerfile
-   To use the Dockerfile: `docker build -f <Dockerfile>`

```sh
docker build
 -t my-app:1.0   # <repo_name:tag>
 .   # '.' for current directory
```

### `FROM` (REQUIRED)

-   General format: `FROM [--platform=<platform>] <IMG>[:<TAG>] [AS <NAME>]`
-   Initializes a new build stage so valid Dockerfile must start with a `FROM` instruction

    -   `ARG` is the only instruction that may precede `FROM` in the Dockerfile

    ```bash
    # FROM instructions support variables that are declared by any ARG instructions that occur before the first FROM.

    ARG  CODE_VERSION=latest
    FROM base:${CODE_VERSION}
    CMD  /code/run-app

    FROM extras:${CODE_VERSION}
    CMD  /code/run-extras

    # An ARG declared before a FROM is outside of a build stage, so it cannot be used after FROM.
    # To do so, use an ARG instruction without a value inside of a build stage:

    ARG VERSION=latest
    FROM busybox:$VERSION
    ARG VERSION
    RUN echo $VERSION > image_version
    ```

-   Also set the base image to launch to interim container (where all other instructions execute in) during the build process
-   When selecting base images, consider images with package managers for an easier time i.e. apt, yum are one of the reasons to build containers FROM debian, Ubuntu, Fedora, CentOS

### `ENV`

-   General format: `ENV <KEY>=<VALUE>  <KEY>=<VALUE> ...`
-   Set environment variables that persists through image build process and into the final image.
    -   Use `ARG` if the variables are only needed for the image build
-   Preferred way to inject key/value since they work everywhere, on every OS and config
-   Can be changed using `docker container run --env <KEY>=<VALUE>`

### `WORKDIR`

-   General format: `WORKDIR <FOLDER_PATH>`
-   Defines the working directory inside the interim container from where all other instructions run i.e. `RUN`, `CMD`, `ENTRYPOINT`, `COPY` and `ADD`
    -   Automatically created if not exists
    -   Also the starting directory when the container is launched using the new image
-   Defaults to `/` if not specified. In practice, if not building from scratch, the WORKDIR likely set by the base image.
    -   To avoid unintended operations in unknown directories, it is best practice to set your WORKDIR explicitly.
-   Can be used multiple times in a Dockerfile. If a relative path is provided, it will be relative to the path of the previous WORKDIR instruction. For example:

```sh
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
# The output of the final pwd command in this Dockerfile would be /a/b/c.
```

### `COPY`

-   General format `COPY <SRC> <DEST_DIR>`
    -   Paths provided can be relative paths
-   `COPY . .`:
    -   1st '.' refers to all files in current working directory relative to the build context
    -   2nd '.' refers to the directory define in `WORKDIR`
-   functionality similar to `docker cp`
-   Copying multiples file ?

#### `COPY` vs `ADD`

-   ADD can support remote sources
-   ADD will extract files after copying files over

### `RUN`

-   Has 2 forms

    -   shell form: `RUN <COMMAND>`

    ```bash
    # Can use a '\' to continue a single RUN instruction onto the next line.
    # Both commands below are the same

    RUN /bin/bash -c 'source $HOME/.bashrc && \
    echo $HOME'

    RUN /bin/bash -c 'source $HOME/.bashrc && echo $HOME'
    ```

    -   exec form: `RUN ["executable", "param1", "param2"]`
        -   Can be use to change shells: `RUN ["/bin/bash", "-c", "echo hello"]`

-   Execute shell commands in the interim container
-   Changes here is committed as a new layer on top of the base image, to be used for the next steps in the Dockerfile.
    -   `RUN` vs `CMD`: RUN executes during the image build process, CMD execute when launching a container
-   Can also run shell scripts that were copied into the container earlier
-   Usually for installing software with a package repository or doing some unzipping, file edits inside the container
-   chaining commands with '`&&`' in order to fit all these changes in one single layer. Saves space and time.
-   NOTE: Proper way to do logging insider a container is to not log to a log file BUT to ensure everything we want to be captured in the logs is spit out to stdout and stderr. Docker will handle to rest.

### `EXPOSE`

-   Defines which ports the container is listening from
-   Can be overwritten using `-p` in `docker container run`
-   Hence, in essence, is really for documentation purposes

### `CMD`

-   Execute shell commands to launch the application when we run a container from the image
-   Only one CMD command is honoured i.e. the latest one
-   Can be overwritten via `docker run`

### `ENTRYPOINT`

-   Has two forms:
    -   exec form (preferred):`ENTRYPOINT ["executable", "param1", "param2"]`
    -   shell form:`ENTRYPOINT command param1 param2`
-   Allows you to configure a container that will run as an executable.
-   Executed right before CMD when a container is launched from an image
-   Used for initialization steps before the final app launch via CMD
    -   for e.g. MYSQL
        -   ENTRYPOINT: Runs a shell script that sets up the database i.e set USER, PASSWORD, grant permissions, create schemas, close database
        -   CMD: launch application
-   Can be override using `docker run --entrypoint`

#### exec form

-   You can use the exec form of ENTRYPOINT to set fairly stable default commands and arguments and then use either form of CMD to set additional defaults that are more likely to be changed.

```bash
FROM ubuntu
ENTRYPOINT ["top", "-b"]
CMD ["-c"]
```

### Example: Dockerfile for Pyspark unit tests

https://www.confessionsofadataguy.com/introduction-to-unit-testing-with-pyspark/

```raw
FROM ubuntu:18.04

RUN apt-get update && \
    apt-get install -y default-jdk scala wget vim software-properties-common python3.8 python3-pip curl unzip libpq-dev build-essential libssl-dev libffi-dev python3-dev&& \
    apt-get clean

RUN wget https://archive.apache.org/dist/spark/spark-3.0.1/spark-3.0.1-bin-hadoop3.2.tgz && \
    tar xvf spark-3.0.1-bin-hadoop3.2.tgz && \
    mv spark-3.0.1-bin-hadoop3.2/ /usr/local/spark && \
    ln -s /usr/local/spark spark

WORKDIR app
COPY . /app
RUN pip3 install cython==0.29.21 numpy==1.18.5 && pip3 install pytest pyspark pandas==1.0.5
ENV PYSPARK_PYTHON=python3

```
``` yaml
version: "3.9"
services:
  test:
    environment:
      - PYTHONPATH=./src
    image: "spark-test"
    volumes:
      - .:/app
    command: python3 -m pytest    # <-- triggers pytest
```
### Multi-Stage Dockerfile
