`Docker Swarm` is Docker's native clustering and orchestration tool for managing a group of Docker hosts as a single virtual cluster

* Built directly into the Docker Engine via `docker swarm` commands
* Uses a manager-worker node model where managers handle scheduling and workers run containers
* Significantly simpler to set up than Kubernetes but offers fewer features and less ecosystem support
* Services are the [[Deployments]] unit in Swarm; each service defines how many replicas to run

## Swarm Architecture

A Swarm cluster is composed of manager nodes and worker nodes

* **Manager nodes**: maintain cluster state using the [[Architecture/Leader Election]] [[Consensus]] algorithm, schedule tasks, and serve the Swarm API
* **Worker nodes**: execute tasks assigned by the manager; do not participate in Raft
* A cluster can have multiple managers for high availability; one is elected leader via Raft
* Worker nodes communicate with managers over mutual TLS

## Key Swarm Concepts

| Concept | Description |
|---|---|
| `Node` | A Docker host participating in the Swarm |
| `Service` | The definition of tasks to run (image, replicas, ports) |
| `Task` | A running container instance of a service |
| `Stack` | A multi-service application defined via `docker-compose.yml` |

## Basic Commands

```Bash
docker swarm init --advertise-addr NODE_IP
docker swarm join --token TOKEN MANAGER_IP:2377
docker service create --replicas 3 --name SERVICE_NAME IMAGE
docker service ls
docker service scale SERVICE_NAME=5
docker stack deploy -c docker-compose.yml STACK_NAME
docker node ls
```

## Swarm vs Kubernetes

* Swarm is easier to operate but lacks Kubernetes features such as auto-scaling, advanced [[RBAC]], and rich extensibility
* Kubernetes has become the industry standard for production container orchestration at scale

## High Availability

Manager quorum uses Raft, so an odd number of managers is recommended

* **3 managers**: tolerates 1 failure
* **5 managers**: tolerates 2 failures
* Adding more than 7 managers is not recommended — Raft overhead degrades performance
