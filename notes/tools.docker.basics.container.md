---
id: tplcsgn73m3klgljmz6m92j
title: containers
desc: ''
updated: 1699058124961
created: 1699057794771
---
## Containers

### What is Container?

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

### Where do containers live?

- Container Repository
- Public Repository for Docker: DockerHub

### Image vs Container

- An image
  - The application we want to run
  - The actual package / artifact that can be moved around
- A container
  - An instance of that image running as a process
  - the running environment for the Docker Image
  - You can have many containers running off the same immage

### Container vs Virtual Machine

- Virtual Machines share the hardware resources
- Containers shares the same OS Kernel and hardware resources

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

- `docker container run -it --name nginx bash` to give you a terminal inside the new container. Start new container interactively. Use `exit` to stop
- `docker container exec -it` to run additional command via terminal in existing container
