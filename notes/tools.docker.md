---
id: wo52t5kfmgpfqiwtqm9c0ag
title: docker
desc: ""
updated: 1695276811946
created: 1691076022969
---

- Docker Docs: <https://docs.docker.com/reference/>
- python-on-whales docs: <https://gabrieldemarmiesse.github.io/python-on-whales/sub-commands/compose/>

<https://stackoverflow.com/questions/17157721/how-to-get-a-docker-containers-ip-address-from-the-host?page=1&tab=oldest#tab-top>

---

## What is Container?

- A way to package application with all the necessary dependencies and configuration
- Portable aritifact, easily shared and moved around
- Nakes development and deployment more efficient
- one command to install the app
- Technically, made up of layers of images
  - i.e linus base image, application image at the top
  - To run the imsge, use `docker run <image_name>`
  - If the image is not available, it auto downloads from DockerHub.
  - When the image is runned, a container is created
  - When a container is restarted, data is lost (No data persistence itself)

## Where do containers live?

- Container Repository
- Public Repository for Docker: DockerHub

## Docker Image vs Docker Container

- Docker Image is the actual package / artifact that can be moved around
- Docker Container is the running environment for the Docker Image

## Docker vs Virtual Machine

- Virtual Machines share the hardware resources
- Docker shares the same OS Kernel and hardware resources

## Port: Container vs Host

- Multiple containers can run on your host machine
- Host machine has only certain ports available for application
- Conflict when same port on host machine
- Will need to assign a free port with the container

## Docker Compose: Running multiple containers

### Converting the `docker run` command into yaml

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

- docker compose takes care of creating a common network

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

### Running the `docker-compose.yaml`

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

## Dockerfile: Blueprint for Building Docker Images

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

```sh
docker build
 -t my-app:1.0   # <repo_name:tag>
 .   # '.' for current directory
```

## Docker Volumes: Persist data in docker

- To provide data persistence, the file system on the host is plugged into the container's file system
- This means that data written on the container's file system is replicated on the host's file directory

#
