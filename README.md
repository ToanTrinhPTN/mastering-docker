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
FROM node:12-alpine

# Create a directory where our app will be placed
RUN mkdir -p /usr/src/app

# Change directory so that our commands run inside this new directory
WORKDIR /usr/src/app

# Copy dependency definitions
COPY package.json /usr/src/app

# Install dependecies
RUN npm install

# Get all the code needed to run the app
COPY . /usr/src/app

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
FROM openjdk:8-jre-alpine

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
FROM openjdk:8-jre-alpine

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