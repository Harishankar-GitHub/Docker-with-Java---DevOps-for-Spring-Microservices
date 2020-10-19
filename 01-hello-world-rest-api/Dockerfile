# Dockerfile for Dockerfile Maven Plugin - START

FROM openjdk:8-jdk-alpine
EXPOSE 8080
ADD target/*.jar app.jar
ENTRYPOINT ["sh", "-c", "java -jar /app.jar"]

# Dockerfile for Dockerfile Maven Plugin - END

# Dockerfile for Maven Dependency Plugin - START

#FROM openjdk:8-jdk-alpine
#ARG DEPENDENCY=target/dependency
#COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
#COPY ${DEPENDENCY}/META-INF /app/META-INF
#COPY ${DEPENDENCY}/BOOT-INF/classes /app
#ENTRYPOINT ["java","-cp","app:app/lib/*","com.in28minutes.rest.webservices.restfulwebservices.RestfulWebServicesApplication"]

# Dockerfile for Maven Dependency Plugin - END