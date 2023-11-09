---
id: 65mjp3wgb2vdf3wwivlxfmo
title: commands
desc: ''
updated: 1699058406500
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
>   - `--format`: common option for formatting output of commands using GO templates
>- `logs <CONT_NAME`: shows logs for a specific container. Same as `docker logs`
>   - `--help`: see all log options
>- `ls`: list running containers. same as `docker ps`
>- `port <CONT_NAME>`: shows which ports are forwarding traffic to the container
>- `rm <CONT_ID_1 CONT_ID_2 ...>`:
>   - `-f`: force remove
>- `run [OPTIONS]<IMG_NAME> [COMMAND] [ARG...]`: starts a **new** container from an image. same as `docker run`
>   - `--detach`: run it in the background
>   - `--env`: pass in environment variables
>   - `--name <NAME>`: assign name to container
>   - `-p, --publish`: publish ports. Always in HOST:CONTAINER format
>   - `-t`: pseudo tty i.e. simulate a real terminal
>   - `-i, --interactive`: keep session open to receive terminal input
>   - `--network <NETWORK_NAME>`: attach container to network. Default network if unspecified
>- `start`: start an existing stopped container
>- `stats`: show live performance statistics for all containers. Saem as `docker stats`
>- `stop <CONT_ID>` stops the container process w/o removing it. Same as `docker stop`
>- `top <CONT_NAME>`: list the running processes in a container
>
>#### Network: `docker network`
>
>- `ls`: Show all networks created
>- `inspect <NETWORK_NAME>`: show containers attached to that network
>- `create [OPTIONS] <NETWORK_NAME>`: Spawns a new virtual network for you to attach containers to
>   - `--driver`:
>- `connect [OPTIONS] <NETWORK> <CONT>`: Connects a container to a network.
>- `disconnect [OPTIONS] <NETWORK> <CONT>`: Disconnects a container from a network. The container must be running.


![](https://intellipaat.com/blog/wp-content/uploads/2022/10/Docker-Cheat-Sheet-2022.jpg)



``` bash
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



