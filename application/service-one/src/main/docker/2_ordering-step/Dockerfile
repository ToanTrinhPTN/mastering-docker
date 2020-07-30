FROM maven:3.6.1-jdk-8-alpine
LABEL author="Phong Pham"
LABEL maintainer="pnqphong95@gmail.com"

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

# Copy only pom file for fetching dependencies
COPY pom.xml /usr/src/app
# Fetch dependencies first
RUN mvn -e -B dependency:resolve

COPY . /usr/src/app

RUN mvn clean package

EXPOSE 8082
ENTRYPOINT ["java", "-Xmx256m", "-Xss32m", "-Djava.security.egd=file:/dev/./urandom","-jar","target/service-one.jar"]