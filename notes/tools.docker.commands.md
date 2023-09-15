---
id: 65mjp3wgb2vdf3wwivlxfmo
title: CLI
desc: ''
updated: 1694663069723
created: 1694538082966
---

![](https://intellipaat.com/blog/wp-content/uploads/2022/10/Docker-Cheat-Sheet-2022.jpg)


## Docker Commands

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
```



