FROM maven:3.6.1-jdk-8-alpine AS packaging
LABEL author="Toan Trinh"
LABEL maintainer="tttoan0208@gmail.com"

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY pom.xml /usr/src/app
RUN mvn -e -B dependency:resolve
COPY . /usr/src/app
RUN mvn clean package

## Following section used for running application
FROM openjdk:8-jre-alpine as final

COPY --from=packaging /usr/src/app/target/service-one.jar /

EXPOSE 8082
ENTRYPOINT ["java","-jar","/service-one.jar"]

# FROM openjdk:8-jre-alpine

# # environment
# EXPOSE 8082

# # executable ADD @project.artifactId@-@project.version@.jar app.jar
# ADD target/service-one.jar app.jar

# # run app as user 'booter'
# RUN /bin/sh -c 'touch /app.jar'
# ENTRYPOINT ["java", "-Xmx256m", "-Xss32m", "-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
