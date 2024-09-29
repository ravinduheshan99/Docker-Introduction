# Docker Command Summary and Practical Guide

This document provides a summarized guide to the most essential Docker commands for container and image management, along with practical tips for effectively using Docker. It serves as a quick reference for performing Docker operations and setting up environments.

## 1. Basic Container Management
- **Run Containers:**
  - `docker run <image_name>`: Start a container from an image.
  - `docker run -d <image_name>`: Run the container in detached mode (background).
  - `docker run -it <image_name> bash`: Run and start an interactive shell.
  - `docker run -p <host_port>:<container_port> <image_name>`: Map ports between host and container.
  - `docker run -v /host/path:/container/path <image_name>`: Mount a volume between host and container.

- **Inspect & Logs:**
  - `docker inspect <container_id>`: Inspect a container’s configuration and details.
  - `docker logs <container_id>`: View logs of a container to debug issues.

- **List & Remove Containers:**
  - `docker ps`: List running containers.
  - `docker ps -a`: List all containers (including stopped).
  - `docker stop <container_name/id>`: Stop a running container.
  - `docker rm <container_name/id>`: Remove a stopped container.

- **Execute Commands in Running Containers:**
  - `docker exec -it <container_name> <command>`: Run a command inside a running container.

## 2. Image Management
- **Pull & Run Images:**
  - `docker pull <image_name>`: Pull an image from Docker Hub without running it.
  - `docker images`: List available images locally.
  - `docker rmi <image_name>`: Remove an image from local storage.

- **Building Custom Images:**
  - Create a `Dockerfile` specifying the image configuration.
  - `docker build -t <image_tag> .`: Build an image from the current directory using a `Dockerfile`.
  - `docker push <image_tag>`: Push a custom image to a Docker registry.

## 3. Advanced Examples and Practical Tips

### Create a Custom Web App Image
1. Create a `Dockerfile` with the following content:
    ```dockerfile
    FROM ubuntu
    RUN apt-get update
    RUN apt-get install -y python python-pip
    RUN pip install flask
    COPY app.py /opt/app.py
    ENTRYPOINT Flask_APP=app.py flask run --host=0.0.0.0
    ```
2. Build and run the Docker image:
    ```bash
    docker build -t my-simple-webapp .
    docker run -d -p 8282:8080 my-simple-webapp
    ```

### Using ENV and CMD in Dockerfile
1. Example `Dockerfile` with environment variables and commands:
    ```dockerfile
    FROM python:3.6-alpine
    WORKDIR /app
    COPY . /app
    RUN pip install --no-cache-dir -r requirements.txt
    EXPOSE 8080
    ENV NAME WebAppColor
    CMD ["python", "app.py"]
    ```
2. Build and run the Docker image:
    ```bash
    docker build -t webapp-color:lite .
    docker run -d -p 8282:8080 webapp-color:lite
    ```

## 4. Practical Tips for Effective Docker Use
- **Always Use Dockerfile for Consistency:** Creating a `Dockerfile` ensures that your image builds are reproducible and minimizes configuration errors.
- **Use Named Volumes for Persistent Data:** Prevent data loss when containers are recreated using named volumes.
  ```bash
  docker run -v my_volume:/data my_image

## 5. Docker Networking & Port Mapping
- Inspect Network Configuration
  `docker network ls`: List all networks.
  `docker network inspect <network_name>`: Get detailed network information.
- Port Mapping:
  Use `-p <host_port>:<container_port>` to map ports between host and container

  ```bash
  docker run -p 80:5000 <image_name>

## 6. Dockerfile Best Practices
- **Order Matters**: Place frequently changing instructions (e.g., COPY, RUN) towards the end of your Dockerfile to leverage Docker's caching mechanism.
- **Minimize Layer Count**: Each instruction (e.g., RUN, COPY) creates a new layer in the image. Combine related commands to reduce the layer count.

  ```bash
  RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip

- **Use Multi-Stage Builds**: For complex applications, consider using multi-stage builds to reduce the final image size:

  ```bash
  FROM golang:1.16 AS builder
  WORKDIR /app
  COPY . .
  RUN go build -o main .

  FROM alpine
  COPY --from=builder /app/main /main
  CMD ["/main"]
