FROM maven:3.6.3-adoptopenjdk-8 AS MAVEN_BUILD
WORKDIR /build/

COPY pom.xml ./
RUN mvn -e -B dependency:resolve

COPY src ./src/
RUN mvn -e -B package

FROM adoptopenjdk/openjdk8-openj9:alpine-jre
WORKDIR /app

COPY --from=MAVEN_BUILD /build/target/*.jar /app/app.jar
VOLUME /app/config

ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom", "-jar","/app/app.jar"]

EXPOSE 39000