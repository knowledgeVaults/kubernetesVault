[[Docker]] is a platform for building, shipping, and running containers using a client-server architecture

* Packages applications and their dependencies into portable container images
* Used as a [[Container Runtime]] in Kubernetes before being replaced by [[containerd]]-based runtimes
* Docker containers share the host OS kernel, making them lightweight compared to virtual machines
* Images are layered using a Union File System (overlay2 by default), allowing efficient reuse

## Docker Architecture

Docker follows a client-daemon model where all container operations go through a central daemon

* **Docker CLI** (`docker`): the client that sends commands to the daemon via REST API
* **Docker Daemon** (`dockerd`): background service managing images, containers, volumes, and networks
* **containerd**: embedded within Docker, handles lower-level image and container lifecycle
* **runc**: the OCI-compliant runtime that actually spawns the container process

## Docker Image

A Docker image is a read-only layered template used to create containers

* Built from a `Dockerfile` using `docker build`
* Stored in registries such as Docker Hub or private registries
* Each instruction in a Dockerfile creates a new immutable layer

```Bash
docker build -t IMAGE_NAME:TAG .
docker push IMAGE_NAME:TAG
docker pull IMAGE_NAME:TAG
docker images
```

## Docker Container

A container is a running instance of a Docker image with its own isolated process namespace

```Bash
docker run -d --name CONTAINER_NAME IMAGE_NAME
docker ps
docker stop CONTAINER_NAME
docker rm CONTAINER_NAME
docker logs CONTAINER_NAME
docker exec -it CONTAINER_NAME /bin/sh
```

## Docker Networking

Docker manages container networking via virtual bridges and network drivers

* **bridge**: default network; containers communicate via a virtual bridge (`docker0`)
* **host**: container shares the host's network stack directly
* **none**: no networking configured; fully isolated
* **overlay**: spans multiple Docker hosts (used by Docker Swarm)
