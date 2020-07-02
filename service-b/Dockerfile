FROM maven:3.6.3-adoptopenjdk-8 AS MAVEN_BUILD
WORKDIR /build/

COPY pom.xml ./
RUN mvn -e -B dependency:resolve

COPY src ./src/
RUN mvn -e -B package

FROM adoptopenjdk/openjdk8-openj9:alpine-jre
RUN apk update
RUN apk add --no-cache openssl
RUN apk add ca-certificates
RUN update-ca-certificates

ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz

WORKDIR /app

COPY --from=MAVEN_BUILD /build/target/*.jar /app/app.jar
VOLUME /app/config

ENTRYPOINT ["dockerize", "-timeout", "5m", "-wait", "tcp://rabbitmq:5672","java","-Djava.security.egd=file:/dev/./urandom", "-jar","/app/app.jar"]

EXPOSE 38000