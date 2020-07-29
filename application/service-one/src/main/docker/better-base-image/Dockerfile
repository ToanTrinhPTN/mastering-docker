FROM maven:3.6.1-jdk-8-alpine
LABEL author="Phong Pham"
LABEL maintainer="pnqphong95@gmail.com"

# Create a directory where our app will be placed
RUN mkdir -p /usr/src/app

# Change directory so that our commands run inside this new directory
WORKDIR /usr/src/app

# Copy pom file to fetch dependencies first
COPY pom.xml /usr/src/app

# Fetch all dependencies
RUN mvn -e -B dependency:resolve

# Copy all the code needed to build the app
COPY . /usr/src/app

# Build application
RUN mvn clean package

# environment
EXPOSE 8082

# Run application
ENTRYPOINT ["java", "-Xmx256m", "-Xss32m", "-Djava.security.egd=file:/dev/./urandom","-jar","target/service-one*.jar"]