## Configuration for Spring boot Application

Create below docker image to run the spring boot application as well as add the add the elastic
APM agent to application.

FROM openjdk:8-jdk

EXPOSE 8080
RUN mkdir -p /opt/app
WORKDIR /opt/app

ARG JAR_PATH

COPY $JAR_PATH /opt/app

RUN wget -O apm-agent.jar https://search.maven.org/remotecontent?filepath=co/elastic/apm/elastic-apm-agent/1.2.0/elastic-apm-agent-1.2.0.jar

CMD java -javaagent:/opt/app/apm-agent.jar $JVM_OPTIONS -jar $JAR_NAME

###########################################################################

##  Create a docker-compose  for micro-service to listen elastic apm server.

Example micro-service name : user-service

DockerCompose file:
===================

user-microservice:
    image: apm-java/app:1.0.0
    build:
      context: ../
      dockerfile: docker/java/Dockerfile
      args:
        - "JAR_PATH=target/apm-0.0.1-SNAPSHOT.jar"
    environment:
      - "JAR_NAME=apm-0.0.1-SNAPSHOT.jar"
      - "JVM_OPTIONS="
      - "ELASTIC_APM_SERVICE_NAME=user-service"
      - "ELASTIC_APM_APPLICATION_PACKAGES=com.cosmin.tutorials.apm"
      - "ELASTIC_APM_SERVER_URLS=http://apm:8200"
    ports:
      - 8080:8080
    links:
      - apm
      - mysql


# Check the logs and reports of APM

Example elastic APM server address  - htt://localhost:5601
 
 