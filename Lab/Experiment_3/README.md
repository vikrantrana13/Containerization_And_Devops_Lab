## EXPERIMENT – 3

## Deploying NGINX Using Different Base Images and Comparing Image Layers
---

## Aim

To deploy and analyze NGINX using different Docker base images (official, Ubuntu, and Alpine) in order to understand image layers, size differences, performance, security, and real-world containerized use cases.

---

## Objectives

- Deploy NGINX using:
  - Official nginx image
  - Ubuntu-based image
  - Alpine-based image
- Understand Docker image layers and size differences
- Compare performance, security, and use-cases of each approach
- Explain real-world use of NGINX in containerized systems

---

## Prerequisites


- Docker installed and running
- Basic knowledge of:
   - docker run
   - Dockerfile
   - Port mapping
- Linux command basics

## Theory

- **NGINX** is a high-performance, event-driven web server that is widely used for serving static content, reverse proxying, load balancing, and SSL termination. In containerized environments, NGINX is commonly deployed using Docker images to ensure portability, scalability, and consistent configuration across systems.
- Docker images are built in layers, and the choice of **base image significantly affects image size, security surface, startup time, and performance**.
- The _official NGINX image_ is optimized and production-ready, providing a balanced combination of stability and ease of use. - _Ubuntu-based images_ are larger because they include a full operating system with additional utilities, making them suitable for debugging and learning but less ideal for production. 
- _Alpine-based images_ are minimal and lightweight, resulting in smaller image sizes, faster pull times, and reduced attack surface, making them suitable for microservices and cloud-native deployments.
- Understanding the differences between these base images helps in selecting the appropriate deployment strategy based on use case, performance requirements, and security considerations in real-world containerized systems.

---

## Software Requirements

- macOS / Windows OS  
- Docker Desktop installed and running  
- Internet connection  

---

## Procedure / Steps to Perform the Experiment


## Part 1: Deploy NGINX Using Official Image

### 1.Pull the Image
```bash
docker pull nginx:latest
```
![pull](images-1.png)

- Understanding the output

- `docker.io`
This is the registry (Docker Hub’s official registry).
If you don’t specify one, Docker automatically uses docker.io.
- `library`
This is the default namespace for official images.
Official images like nginx, ubuntu, mysql are stored under library.
- `nginx`
The image name.
- `latest`
The tag (version). If you don’t specify a tag, Docker assumes latest.

### 2. Run the Container

```bash
docker run -d --name nginx-official -p 8080:80 nginx
```

![run](images-2.png)

### 3. Verify Running Containers 

```bash 
curl http://localhost:8080
```
![verify](images-3.png)
![web](images-4.png)

- HTML code visible hence verified

## KEY OBSERVATIONS 

```bash 
docker images nginx
```
- Image is pre-optimized  
- Minimal configuration required  
- Uses Debian-based OS internally  

![key](images-5.png)

## Part 2: Custom NGINX Using Ubuntu Base Image

- Instead of using the ready-made nginx image, we will:
  - Start from Ubuntu
  - Install nginx manually
  - Build our own image
  - Run it
- This helps you understand how Docker images are built.

### 1. Create a New Folder

```bash
mkdir nginx-ubuntu
cd nginx-ubuntu
```
![key](images-6.png)

### 2. Create Docker File 

- Inside the folder ngnix-ubuntu
```bash
nano Dockerfile
```
![nano](images-7.png)


- Paste the following : 
 ```bash 
 FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y nginx && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```
- Press Control + O
- Press Enter
- Press Control + X to exit

- To view the contents of the file type 
```bash
cat Dockerfile
```
![cat](images-8.png)


### 3. Build the Image

```bash
docker build -t nginx-ubuntu .
```
![build](images-9.png)

### 4. Run Container 

```bash
docker run -d --name nginx-ubuntu -p 8081:80 nginx-ubuntu
```

![runing](images-10.png)


## KEY OBSERVATIONS

- Check Image Size
```bash
docker images | grep nginx
```
![size](images-11.png)

**IMAGE SIZE COMPARISON**
- nginx (official) : Larger
- nginx-ubuntu : Samller
- _NOTE_ : Generally full OS images are larger but this did not happen in this case because, the official NGINX image is Debian-based and includes additional modules, default configurations, optimizations, and multiple filesystem layers. In contrast, the Ubuntu-based image we built manually installed only the required NGINX package and removed unnecessary package lists and cache files, reducing its final size.

## Part 3 : Custom NGINX Using Alpine Base Image

- Instead of Ubuntu, we will:
  - Use Alpine Linux (very small OS)
  - Install nginx using apk
  - Build image
- Compare size

### 1. Create a New Folder

```bash
cd ~
mkdir nginx-alpine
cd nginx-alpine
```
![key](images-12.png)

### 2. Create Docker File 

- Inside the folder ngnix-alpine
```bash
nano Dockerfile
```
![nano](images-13.png)


- Paste the following : 
 ```bash 
 FROM alpine:latest

RUN apk add --no-cache nginx

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

```
- Press Control + O
- Press Enter
- Press Control + X to exit



### 3. Build the Image

```bash
docker build -t nginx-ubuntu .
```
![build](images-14.png)

### 4. Run Container 

```bash
docker run -d --name nginx-ubuntu -p 8081:80 nginx-ubuntu
```

![runing](images-15.png)


## KEY OBSERVATIONS

- Check Image Size
```bash
docker images | grep nginx
```
- You can very well see the alpine image
  - Extremely small image
  - Fewer packages
  - Faster pull and startup time
![size](images-16.png)

## Part 4 : Image size and Comparison

## Image Size and Comparison Table

| Image Name              | Image ID        | Virtual Size | Actual Size | Notes |
|--------------------------|----------------|-------------|-------------|-------|
| nginx-alpine:latest     | 5c460ed1ecac   | 16.7MB      | 5.08MB      | Smallest image (custom Alpine build) |
| nginx-ubuntu:latest     | 28a6ac785e50   | 187MB       | 50.5MB      | Ubuntu-based custom build |
| nginx:alpine            | 1d13701a5f9f   | 92.6MB      | 26.7MB      | Official Alpine variant |
| nginx:latest            | 341bf0f3ce6c   | 258MB       | 64.1MB      | Official Debian-based image (largest) |

### Comparison Summary

- `nginx-alpine:latest` is the smallest due to minimal Alpine base and manual optimization.
- `nginx-ubuntu:latest` is larger because it includes a full Ubuntu base.
- `nginx:alpine` is optimized but still larger than the custom Alpine build.
- `nginx:latest` is the largest as it is Debian-based and includes additional modules and layers.

## Part 5 : Inspect Layers

```bash
docker history nginx
docker history nginx-ubuntu
docker history nginx-alpine
```

![inspect](images-17.png)

### Observations:

- Ubuntu has many filesystem layers  
- Alpine has minimal layers  
- Official NGINX image is optimized but heavier than Alpine  

## Part 6 : Functional Tasks Using NGINX

### Task 1: Serve Custom HTML Page

1. Create a Folder for HTML
```bash
cd ~
mkdir html
cd html
```
2. Create a Custom HTML File

```bash
echo "<h1>Hello from Docker NGINX</h1>" > index.html
```

3. Run NGINX with Volume Mapping

- Go back to home directory
```bash
cd ~
```

```bash
docker run -d \
  --name nginx-custom \
  -p 8083:80 \
  -v $(pwd)/html:/usr/share/nginx/html \
  nginx
```
![merge](images-18.png)



4. Verify
- Search this on web browser<br>
`http://localhost:8083`

![browser](images-19.png)










## Insights Gained 

- Successfully deployed NGINX using three approaches: official image, Ubuntu-based image, and Alpine-based image.
- Understood how base images significantly impact Docker image size and layer structure.
- Observed that Alpine-based images are the smallest due to minimal OS footprint.
- Learned that Ubuntu-based images provide more system utilities but increase image size and attack surface.
- Noted that official NGINX images are production-ready and pre-optimized.
- Compared image layers using `docker history` to understand filesystem layering.
- Practiced port mapping and verified container functionality using `curl`.
- Understood the importance of running NGINX in foreground mode using `daemon off;` inside containers.
- Identified real-world use cases of NGINX such as reverse proxy, load balancing, SSL termination, and static content hosting.
- Concluded that base image selection depends on use case: Alpine for lightweight deployments, official image for production, and Ubuntu for learning/debugging environments.



## RESULT 

Docker images were successfully pulled, containers executed, and lifecycle commands performed.

## Challenges
- _apk add_ command was not working because I had not built the image yet and I was runnig it on my Mac terminal.
- Custom made image was larger in size then Ubuntu OS image.
