---
id: dtm4r38cjc01v8fbuy08891
title: Vscode
desc: ''
updated: 1698985941091
created: 1691624185047
---

- [Docker docs](docs.docker.com)

## Commands

- General structure:

  ``` bash
  # new "management commands" format
  docker <command> <sub-command> (options)
  ```

- Note that the old method still works: `docker <command> (options)`
- for e.g. `docker run` is same as `docker container run`

>### Reference
>
>#### System
>
>- `docker version`: check your version and that docker is working
>- `docker info`: shows most config values for the docker engine
>- `ps aux`: show me all running processes
>
>#### Containers: `docker container`
>
>- `exec <CONT_NAME>`: run additional process in running container
>- `inspect <CONT_NAME>`: show metadata about the container. Same as `docker inspect`
>- `logs <CONT_NAME`: shows logs for a specific container. Same as `docker logs`
>   - `--help`: see all log options
>- `ls`: list running containers. same as `docker ps`
>   - `--env`: pass in environment variables
>- `rm <CONT_ID_1 CONT_ID_2 ...>`:
>   - `-f`: force remove
>- `run [OPTIONS]<IMG_NAME> [COMMAND] [ARG...]`: starts a **new** container from an image. same as `docker run`
>   - `--detach`: run it in the background
>   - `--name <NAME>`: assign name to container
>   - `-t`: pseudo tty i.e. simulate a real terminal
>   - `-i, --interactive`: keep session open to receive terminal input
>- `start`: start an existing stopped container
>- `stats`: show live performance statistics for all containers. Saem as `docker stats`
>- `stop <CONT_ID>` stops the container process w/o removing it. Same as `docker stop`
>- `top <CONT_NAME>`: list the running processes in a container

## Containers

### Image vs Container

- An image is the application we want to run
- A container is an instance of that image running as a process
- You can have many containers running off the same immage

### Starting Docker Containers

- Start a new container from an image using the `docker container run` command
- `docker container run --publish 80:80 --detach nginx`
  1. looks for image locally. if it doesn't find it, looks in Docker Hub
  2. Downloads image from Docker Hub (default: latest version)
  3. Creates new container from that image
  4. Opens port 80 on the host IP
  5. Routes traffic to the container IP port 80
  6. Starts container by using the CMD in the image Dockerfile
  - Note that if host port(left number) is being used, you'll get a "bind" error

### Monitoring Containers

- `docker container top` to show process list in one container
- `docker container inspect` to show details of one container config
- `docker container stats` for live performance stats for all containers

### Getting a Shell

- `docker container run -it --name nginx bash` to give you a terminal inside the running container. Use `exit` to stop
