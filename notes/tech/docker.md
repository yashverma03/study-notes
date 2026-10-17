# Index

- [Intro](#intro)
  - [CI/ CD](#ci-cd)
- [Docker](#docker)
  - [Docker commands](#docker-commands)
  - [Dockerfile](#dockerfile)
  - [Docker swarm](#docker-swarm)


# Intro


## CI/ CD

- automated practices that make software development, testing, and deployment faster, safer, and more reliable
- CI (Continuous Integration)- Automatically build & test code when developers push changes
- CD (Continuous Delivery)- Automatically prepare code for release (manual approval to deploy to prod, automatically deployed to staging)
- CD (Continuous Deployment)- Automatically deploy code to production (no manual step)


# Docker

containerization platform that lets you package, ship, and run applications in isolated containers

Components

- Docker Engine- core part of docker, that handles the creation and management of containers
- Docker Daemon- core background service that powers all Docker functionality on your system
- Docker Image- read-only template that is used for creating containers, containing the application code and dependencies
- Dockerfile- file that describes the steps to create an image
- Docker Hub- cloud based repository that is used for finding and sharing the container images
- Docker Registry- storage distribution system for docker images, where you can store the images in both public and private modes
- Container- A running instance of an image (read-write)
- Volumes- Persistent data storage for containers
- Networks- Connect containers together. Types are bridge, host, overlay, non, macvlan

| Docker Containers | Virtual Machines |
| --- | --- |
| Lightweight and fast | Heavyweight |
| Small size | Large size |
| OS-level virtualization | hardware-level virtualization |
| No hypervisor required | hypervisor required |
| Shares host OS kernel | Each VM has its own full OS |
| Efficient, low CPU/RAM overhead | High resource usage (needs full OS per VM) |

Hypervisor- software that allows you to run multiple virtual machines (VMs) on a single physical host by abstracting and managing hardware resources


## Docker commands

| Method | Description |
| --- | --- |
| `docker build -t <tag-name>` | Build image from Dockerfile in current directory |
| `docker images` | Show all locally available Docker images |
| `docker rmi <image_id>` | Delete image by ID or name |
| `docker pull <image>` | Download image from Docker Hub |
| `docker push <image>` | Upload image to Docker Hub or registry |
| `docker run <image>` | Run container from image |
| `docker run -p 8080:3000 <image>` | Map container port 3000 to host port 8080 |
| `docker run -v $(pwd):/app <image>` | Mount current dir to /app inside container |
| `docker ps` | Show running containers |
| `docker ps -a` | Show all containers (including stopped) |
| `docker stop <container_id>` | Gracefully stop a running container |
| `docker start <container_id>` | Start an existing container |
| `docker restart <id>` | Restart a container |
| `docker rm <container_id>` | Delete a container |
| `docker exec -it <id> bash` | Access running container via shell |
| `docker logs <id>` | View stdout logs from container |
| `docker volume ls` | Show all Docker volumes |
| `docker volume create <name>` | Create a named volume |
| `docker volume rm <name>` | Delete a volume |
| `docker network ls` | Show all networks |
| `docker network create <name>` | Create custom Docker network |
| `docker-compose up` | Start all services in docker-compose.yml |
| `docker-compose down` | Stop and remove services/containers |
| `docker-compose up --build` | Rebuild images before starting |
| `docker-compose logs -f` | Show real-time logs from services |


## Dockerfile

Dockerfile

```dockerfile
# Use official Node image
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy app source code
COPY . .

# Expose port and run app
EXPOSE 3000
CMD ["node", "index.js"]
```

Docker compose

- tool for defining and running multi-container Docker applications
- docker-compose.yml

```yaml
# ================================================
# Docker Compose file for Node.js + MySQL + Redis
# Version: 3.8 (stable for production use)
# ================================================

version: '3.8'

# -------------------------------
# SERVICES (Containers to run)
# -------------------------------
services:

  # ----------------------------------------
  # Node.js application service
  # - Built from local Dockerfile
  # - Loads environment variables from .env
  # - Depends on MySQL and Redis services
  # ----------------------------------------
  app:
    build: .
    container_name: node_app
    ports:
      - "3000:3000"
    env_file:
      - .env
    depends_on:
      - db
      - redis

  # ----------------------------------------
  # MySQL database service
  # - Uses official MySQL 8 image
  # - Loads credentials and database name from .env
  # - Persists data using a named volume
  # ----------------------------------------
  db:
    image: mysql:8
    container_name: mysql_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
      MYSQL_DATABASE: ${DB_NAME}
    ports:
      - "3306:3306"
    volumes:
      - db-data:/var/lib/mysql

  # ----------------------------------------
  # Redis service
  # - Uses official Redis image
  # - Default config is enough for basic caching
  # ----------------------------------------
  redis:
    image: redis:7
    container_name: redis_server
    ports:
      - "6379:6379"

# -------------------------------
# Named volumes for persistence
# -------------------------------
volumes:
  db-data:
```


## Docker swarm

Docker's native container orchestration tool that lets you manage a group of Docker nodes as a single virtual system

Components

- Manager Node- Controls the Swarm, schedules containers, handles cluster state
- Worker Node- Runs containers assigned by the manager
- Service- Describes how a container should run (image, ports, replicas)
- Task- A single running instance of a container
- Overlay Network- Allows containers across nodes to communicate
- Load Balancer- Automatically distributes traffic among service replicas

| Method | Description |
| --- | --- |
| `docker swarm init` | Initialize a Swarm on the current node (as a manager) |
| `docker node ls` | Shows all nodes in the swarm (managers/workers) |
| `docker service ls` | Show all running services |
| `docker stack deploy -c docker-compose.yml mystack` | Deploy a full multi-service app |
| `docker stack ls` | Show all deployed stacks |

| Docker Swarm | Kubernetes (K8) |
| --- | --- |
| No autoscaling | autoscaling |
| Simple overlay network | Native Ingress controllers for external HTTP routing |
