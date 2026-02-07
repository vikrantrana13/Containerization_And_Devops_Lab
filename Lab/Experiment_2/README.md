# Experiment 2

## Title
Docker Installation, Configuration, and Running Images

---

## Objective
To pull Docker images, run containers with port mapping, and manage the container lifecycle using Docker commands.

---

## Tools Used
- Docker
- Docker Desktop
- macOS Terminal
- Nginx Docker Image

---

## Theory
Docker is a containerization platform that allows applications to run in isolated environments called containers. Docker images act as templates for containers. Containers can be started, stopped, and removed easily using Docker lifecycle commands.

---

## Procedure

### Step 1: Pull Docker Image
The Nginx Docker image was pulled from Docker Hub.

---
![PULL DOCKER IMAGES ](./images/image1.jpeg)

### Step 2: Run Docker Container with Port Mapping
A Docker container was started using the Nginx image with port mapping.

---
![RUN CONTAINER](./images/image2.jpeg)

### Step 3: Verify Running Containers
The list of running containers was checked.

---
![VERIFY](./images/image3.jpeg)
### Step 4: Stop , Remove Docker Container and Remove Docker Image
The running container was stopped and removed.
The Docker image was removed from the system.

---
![STOP AND REMOVE CONTAINER](./images/image4.jpeg)

## Commands Used

```bash
docker pull nginx
docker run -d -p 8080:80 nginx
docker ps
docker stop <container_id>
docker rm <container_id>
docker rmi nginx
```

## Result

Docker images were successfully pulled, containers were executed with port mapping, and container lifecycle commands were performed successfully.