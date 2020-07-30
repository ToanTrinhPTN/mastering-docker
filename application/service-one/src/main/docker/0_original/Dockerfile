FROM ubuntu:latest
LABEL author="Phong Pham"
LABEL maintainer="pnqphong95@gmail.com"

# Install jdk, maven
RUN apt-get update && apt-get install -y --no-install-recommends \
    maven \
    openjdk-8-jdk \
    && rm -rf /var/lib/apt/lists/*

# Create a directory where our app will be placed
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

# Copy all the code needed to build the app
COPY . /usr/src/app

# Build application
RUN mvn clean package

# Define entry point which can be executed when container start
EXPOSE 8082
ENTRYPOINT ["java", "-Xmx256m", "-Xss32m", "-Djava.security.egd=file:/dev/./urandom","-jar","target/service-one.jar"]