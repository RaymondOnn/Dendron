---
id: 7rxwjt3b4edz11nojfvhmcs
title: drivers
desc: ''
updated: 1707426130315
created: 1707425338417
---

### The Bridge Driver
- See [[tools.devops.containers.docker.basics.network]]

### The Host Driver
- Host drivers use the networking provided by the host machine And it removes network isolation between the container and the host machine where Docker is running. 
- For e.g. With host networking, running a container on port 80 results the container’s app being available on port 80 on the host’s IP address. 
- Use if you don’t want to rely on Docker’s networking but instead rely on the host machine networking.
- Will require a Linux host to use it since it does't work on Docker desktop

``` sh
# Start an Nginx image and listen to port 80 on the host machine:
# Access Nginx by hitting the http://localhost:80/ url.
docker run --rm -d --network host --name my_nginx nginx
```

- The downside with the host network is that you **can’t run multiple containers on the same host having the same port**. 
  - Ports are shared by all containers on the host machine network.

### The None Driver
- The none network driver does not attach containers to any network. 
- Containers do not access the external network or communicate with other containers. 
- Use when you want to disable the networking on a container.

### The Overlay Driver
- for multi-host network communication, as with Docker Swarm or Kubernetes. 
- It allows containers across the host to communicate with each other without worrying about the setup. Think of an overlay network as a distributed virtualized network that’s built on top of an existing computer network.

- To create an overlay network for Docker Swarm services:
    ``` sh
    docker network create -d overlay my-overlay-network
    ```
- To create an overlay network so that standalone containers can communicate with each other:
    ``` sh
    docker network create -d overlay --attachable my-attachable-overlay
    ```
### The Macvlan Driver
- This driver connects Docker containers directly to the physical host network. 
- As per the Docker documentation:
    >“Macvlan networks allow you to assign a MAC address to a container, making it appear as a physical device on your network. The Docker daemon routes traffic to containers by their MAC addresses. Using the macvlan driver is sometimes the best choice when dealing with legacy applications that expect to be directly connected to the physical network, rather than routed through the Docker host’s network stack.”

- Macvlan networks are best for legacy applications that need to be modernized by containerizing them and running them on the cloud because they need to be attached to a physical network for performance reasons. 
- A macvlan network is also not supported on Docker desktop for macOS.