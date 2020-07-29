FROM ubuntu:latest
LABEL author="Phong Pham"
LABEL maintainer="pnqphong95@gmail.com"

# Install JDK8, maven
RUN apt-get update && apt-get install -y --no-install-recommends \
    maven \
    openjdk-8-jdk 

# Create a directory where our app will be placed
RUN mkdir -p /usr/src/app

# Change directory so that our commands run inside this new directory
WORKDIR /usr/src/app

# Copy all the code needed to run the app
COPY . /usr/src/app

# Build application
RUN mvn clean package

# environment
EXPOSE 8082

# Run application
ENTRYPOINT ["java", "-Xmx256m", "-Xss32m", "-Djava.security.egd=file:/dev/./urandom","-jar","target/service-one*.jar"]