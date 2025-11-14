#Etapa 1: build
FROM gradle:jdk25-alpine AS build

WORKDIR /app

COPY build.gradle.kts .
COPY gradlew .
COPY gradle gradle
COPY src src

ARG DOCKER_HOST_ARG=tcp://host.docker.internal:2375
ENV DOCKER_HOST=$DOCKER_HOST_ARG

RUN ./gradlew build

#Etapa 2: NGINX estudiante
FROM nginx:latest AS estudiante

WORKDIR /app

RUN rm -rf /usr/share/nginx/html/*

COPY --from=build /app/src/estudiante/index.html /usr/share/nginx/html

#Etapa 3: APACHE dni
FROM httpd:latest AS dni

WORKDIR /app

RUN rm -rf /usr/local/apache2/htdocs/*

RUN echo 'Include conf/httpd-auth.conf' >> /usr/local/apache2/conf/httpd.conf

COPY --from=build /app/src/dni/index.html /usr/local/apache2/htdocs

#Etapa 4: API Java
FROM eclipse-temurin:25-jre-alpine AS run

WORKDIR /app

COPY --from=build /app/build/libs/*SNAPSHOT.jar /app/examenProxy-0.0.1-SNAPSHOT.jar

ENTRYPOINT ["java", "-jar", "/app/examenProxy-0.0.1-SNAPSHOT.jar"]