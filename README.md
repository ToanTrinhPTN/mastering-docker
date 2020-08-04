# Mastering Docker

### Part 1: Overview

#### Use case

Dockerize an application



#### Application architecture

<architecture image>



#### Technology

- Spring Boot - Application framework
- Zuul - API Gateway (Load Balancer)
- Consul - Service registration and Discovery
- RabbitMQ - asynchronous microservices messaging.
- Angular, Bootstrap & jQuery - HTML enhanced for web apps!
- Swagger - API documentation



#### Tools

Ensure your machine installed:

- Git
- JDK8
- Maven
- NodeJS
- Docker Desktop



### Part 2: Create Docker images

**Step 1: Clone the repository**

Repository: <tbd>

Stay on checked out folder and execute following command



**Step 2: Build web-application image**

Create Dockerfile

```bash
$ cd application
$ mvn clean package 
$ cd web-application
$ nano Dockerfile
```

Write this content to the file

```dockerfile
# Create image based on the official Node 12 image from dockerhub
FROM node:12

# Create a directory where our app will be placed
RUN mkdir -p /usr/src/app

# Change directory so that our commands run inside this new directory
WORKDIR /usr/src/app

# Get all the code needed to run the app
COPY . /usr/src/app

# Install dependecies
RUN npm install

# Expose the port the app runs in
EXPOSE 4200

# Serve the app
CMD ["npm", "start"]
```

Build the image

```bash
$ docker build -t nginx:1.18.0-alpine .
```



**Step 3: Build service-two image**

Create Dockerfile

```bash
$ cd ../service-two
$ nano Dockerfile
```

Write this content to the file

```dockerfile
FROM openjdk:8-jre

# environment
EXPOSE 8084

# executable ADD @project.artifactId@-@project.version@.jar app.jar
ADD target/service-two.jar app.jar

# run app as user 'booter'
RUN /bin/sh -c 'touch /app.jar'
ENTRYPOINT ["java", "-Xmx256m", "-Xss32m", "-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

Build the image

```bash
$ docker build -t build_service-two:latest .
```



**Step 4: Build service-one image**

Create Dockerfile

```bash
$ cd ../service-one
$ nano Dockerfile
```

Write this content to the file

```dockerfile
FROM openjdk:8-jdk

# environment
EXPOSE 8084

# executable ADD @project.artifactId@-@project.version@.jar app.jar
ADD target/service-one.jar app.jar

# run app as user 'booter'
RUN /bin/sh -c 'touch /app.jar'
ENTRYPOINT ["java", "-Xmx256m", "-Xss32m", "-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

Build the image

```bash
$ docker build -t build_service-one:latest .
```



**Step 5: Build api-gateway image**

Create Dockerfile

```bash
$ cd ../api-gateway
$ nano Dockerfile
```

Write this content to the file

```dockerfile
FROM openjdk:8-jre-alpine

# environment
EXPOSE 8080

# executable ADD @project.artifactId@-@project.version@.jar app.jar
ADD target/api-gateway.jar app.jar

# run app as user 'booter'
RUN /bin/sh -c 'touch /app.jar'
ENTRYPOINT ["java", "-Xmx256m", "-Xss32m", "-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

Build the image

```bash
$ docker build -t build_api-gateway:latest .
```



### Part 3: Create Docker networks and volumes

**Step 1: Create Docker networks**

```bash
$ docker network create -d bridge build_backend
$ docker network create -d bridge build_frontend
```



**Step 2: Create Docker volumes**

```bash
$ docker volume create build_mongodata
```



### Part 4: Run Docker containers



### Part 5: Optimize Docker images

**Optimize 1: Don't install unnecessary dependencies**

Using a --no-install-recommends when apt-get installing packages. This will result in a smaller image size.

```dockerfile
RUN apt-get update && apt-get install -y --no-install-recommends
```

**Optimize 2: Remove apt library cache**

Use of apt-get update should be paired with rm -rf /var/lib/apt/lists/* in the same layer.

```dockerfile
RUN apt-get update && rm -rf /var/lib/apt/lists/*
```

**Optimize 3: Use .dockerignore file**

Use docker ignore file on the root of context directory to ignore stuff we don't want to keep when building docker image.

```
.dockerignore
.gitignore
*.DS_Store
target/
*.iml
*.md
src/main/docker/
```

**Optimize 4: Choose correct base image**

- Don't use latest tag because it's change frequently.
- Prefer minimal version (use image with _-alpine_ suffix).
- Use official image. Example:

```dockerfile
FROM node:12-alpine ## instead of "FROM node:12"
FROM postgres:9.6-alpine ## instead of "FROM postgres:9.6"
..
```

**Optimize 5: Order matters for caching**

Analyse the source code, move some thing doesn't change frequently to top layers.

![Docker layer caching](https://dzone.com/storage/temp/13029811-1581358050914.png)

```dockerfile
# Copy only pom file for fetching dependencies
COPY pom.xml /usr/src/app
# Fetch dependencies first
RUN mvn -e -B dependency:resolve

COPY . /usr/src/app

RUN mvn clean package
..
```

instead of

```dockerfile
COPY . /usr/src/app

RUN mvn clean package
..
```

**Optimize 6: Use multi-stage if needed**

If you are using one container for multi purpose, please consider to break it smaller into multi-stage.

**BAD**
```dockerfile
## Following section used for packaging
FROM maven:3.6.1-jdk-8-alpine 
LABEL author="Phong Pham"
LABEL maintainer="pnqphong95@gmail.com"

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app


COPY pom.xml /usr/src/app

RUN mvn -e -B dependency:resolve

COPY . /usr/src/app

RUN mvn clean package

## Following section used for running application

EXPOSE 8082
ENTRYPOINT ["java","-jar","target/my-service.jar"]
```


**GOOD**
```dockerfile
## Following section used for packaging, create stage packaging
FROM maven:3.6.1-jdk-8-alpine AS packaging
LABEL author="Phong Pham"
LABEL maintainer="pnqphong95@gmail.com"

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app


COPY pom.xml /usr/src/app

RUN mvn -e -B dependency:resolve

COPY . /usr/src/app

RUN mvn clean package

## Following section used for running application
FROM openjdk:8-jre-alpine

COPY --from=packaging /usr/src/app/target/my-service.jar /

EXPOSE 8082
ENTRYPOINT ["java","-jar","/my-service.jar"]
```