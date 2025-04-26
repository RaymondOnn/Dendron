---
id: 65mjp3wgb2vdf3wwivlxfmo
title: commands
desc: ''
updated: 1738078472784
created: 1694538082966
---

## Commands

- General structure:

    ``` bash
    # new "management commands" format
    docker <command> <sub-command> (options)
    ```

- Note that the old method still works: `docker <command> (options)`
- for e.g. `docker run` is same as `docker container run`

<br>

> ### Reference
>
> #### System
>
> - `docker version`: check your version and that docker is working
> - `docker info`: shows most config values for the docker engine
> - `docker login <SERVER_URL>`: Defaults to logging in DockerHub, but can overrider by adding server url
> - `docker logout <SERVER_URL>`: Always logout from shared machines/servers when done to protect your acct
> - `ps aux`: show me all running processes
>
> #### Compose: `docker compose`
>
> - `up <SVC> <SVC>`: Setup volumes/networks and start all containers specified and its dependencies. Builds if images not available
>   - `-d`: detached mode
>   - `--build`: build service before starting
>   - `--profile`: Start services specified under selected profile
> - `down`: Stop all containers and remove containers/volumes/networks
> - `run`:
> - `logs`:
> - `top`: List running processes
> - `build`: Build services
>   - `--no-cache`: disable caching
>   - `--parallel`: build services in parallel
>   - '--pull`: pull image each time building service
> - `images`: List all images built via Compose -`-q`: get only image ids
> - `ps`: List running containers created via Compose
>
> #### Volumes: `docker volume`
>
> - `ls`: Show all volumes available
> - `create [OPTIONS] <VOL_NAME`: Create a named volume. Required to do this before `docker run` to use custom drivers and labels
> - `inspect <VOL_NAME>`: returns JSON metadata about the volume
> - `rm <VOL_NAME>`: remove volumes
> - `prune <IMG>`: Removes all currently unused volumes
>
> #### Images: `docker image`
>
> - `ls`: Show all images downloaded
> - `history <CONT>:<TAG>`: Shows layers of changes made in images. Same as `docker history`
> - `inspect <CONT>`: returns JSON metadata about the image. Same as `docker inspect`
> - `tag <SRC_IMG[:TAG]> <NEW_IMG[:TAG]>`: Assign one or more tags to an image. Default tag is 'latest'. Same as `docker tag`
> - `push <IMG>`: Uploads changed alyers to a image registry. Default: DockerHub
> - `build <WORKDIR>`: Build Docker image. Same as `docker build`
>
> #### Containers: `docker container`
>
> - `exec <CONT_NAME>`: run additional process in running container
> - `inspect <CONT_NAME>`: show metadata about the container. Same as `docker inspect`
>   - `--format`: common option for formatting output of commands using GO templates
> - `logs <CONT_NAME`: shows logs for a specific container. Same as `docker logs`
>   - `--help`: see all log options
> - `ls`: list running containers. same as `docker ps`
> - `port <CONT_NAME>`: shows which ports are forwarding traffic to the container
> - `rm <CONT_ID_1 CONT_ID_2 ...>`: Remove containers
>   - `-f`: force remove
> - `run [OPTIONS]<IMG_NAME> [COMMAND] [ARG...]`: starts a **new** container from an image. same as `docker run`
>   - `--detach`: run it in the background
>   - `--env`: pass in environment variables
>   - `--name <NAME>`: assign name to container
>   - `-p, --publish`: publish ports. Always in HOST:CONTAINER format
>   - `-t`: pseudo tty i.e. simulate a real terminal
>   - `-i, --interactive`: keep session open to receive terminal input
>   - `-v, --volume <HOST_DIR>:<CONT_DIR>:<FLAG>`: bind mount a volume
>   - `--network <NETWORK_NAME>`: attach container to network. Default network if unspecified
> - `start`: start an existing stopped container
> - `stats`: show live performance statistics for all containers. Saem as `docker stats`
> - `stop <CONT_ID>` stops the container process w/o removing it. Same as `docker stop`
> - `top <CONT_NAME>`: list the running processes in a container
>
> #### Network: `docker network`
>
> - `ls`: Show all networks created
> - `inspect <NETWORK_NAME>`: show containers attached to that network
> - `create [OPTIONS] <NETWORK_NAME>`: Spawns a new virtual network for you to attach containers to
>   - `--driver`:
> - `connect [OPTIONS] <NETWORK> <CONT>`: Connects a container to a network.
> - `disconnect [OPTIONS] <NETWORK> <CONT>`: Disconnects a container from a network. The container must be running.

![](https://intellipaat.com/blog/wp-content/uploads/2022/10/Docker-Cheat-Sheet-2022.jpg)

```bash
# download images
docker pull <IMAGE_NAME>

# view available images
docker images

# run docker image (default: attached mode)
docker run
    -d           # for running in detached mode (optional)
    -p<HOST_PORT>:<CONTAINER_PORT>   # for binding/changing ports
    --name     # assigning name to the container as specified
    -e <ENV_KEY: VALUE>  # passing environment variables
    --net <NETWORK_NAME> # docker-network to use
 -v name:/var/lib/mysql/data # docker named volumes <container_directory>
    <IMAGE_NAME>:<VERSION>    # defaults to latest version unless explicitly specified

# Starts particular container
docker rmi <IMAGE_ID>

docker ps           # See all running containers
docker ps -a        # See all running and stopped containers

# Stops particular container
docker stop <CONTAINER_ID>

# Starts particular container
docker start <CONTAINER_ID>

# Delete particular container
docker rm <CONTAINER_ID>


# view logs of container
docker logs <CONTAINER_ID_OR_NAME>

# get terminal access of container
docker exec -it <CONTAINER_ID> /bin/bash
docker exec -it <CONTAINER_ID> /bin/sh # for shell

# exit terminal
exit

# See available docker networks
docker network ls
docker network create <NETWORK_NAME> # create new docker network

docker inspect <CONTAINER_ID>

# Copy files from SRC_PATH to DEST_PATH.
docker cp <SRC_PATH> <CONTAINER_NAME>:<DEST_PATH>
```
